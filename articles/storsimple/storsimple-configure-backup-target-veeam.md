---
title: Série StorSimple 8000 como destino de cópia de segurança com Veeam | Documentos da Microsoft
description: Descreve a configuração de destino de cópia de segurança do StorSimple com Veeam.
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
ms.author: hkanna
ms.openlocfilehash: e7659cca9081834d41f64ef0fbd8ea3686044bfd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012005"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple como um destino de cópia de segurança com Veeam

## <a name="overview"></a>Descrição geral

O Azure StorSimple é uma solução de armazenamento na cloud híbrida da Microsoft. StorSimple resolve as complexidades de crescimento exponencial dos dados, utilizando uma conta de armazenamento do Azure como uma extensão da solução no local e camados automaticamente os dados entre armazenamento no local e o armazenamento na cloud.

Neste artigo, discutimos a integração do StorSimple com Veeam e melhores práticas para integrar as duas soluções. Também podemos fazer recomendações sobre como configurar a Veeam, a melhor integração com o StorSimple. Estamos a diferir a Veeam melhores práticas, arquitetos de cópia de segurança e os administradores para a melhor forma de configurar o Veeam para atender aos requisitos de cópia de segurança individuais e contratos de nível de serviço (SLAs).

Embora ilustraremos passos de configuração e conceitos-chave, este artigo não constitui um guia passo a passo de configuração ou instalação. Partimos do princípio de que os componentes básicos e a infraestrutura estão na ordem de trabalho e está preparado para suportar os conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deve ler isto?

As informações neste artigo serão mais úteis para administradores de cópia de segurança, os administradores de armazenamento e arquitetos de armazenamento que tenham conhecimento de armazenamento, Windows Server 2012 R2, Ethernet, serviços cloud e Veeam.

### <a name="supported-versions"></a>Versões suportadas

-   Veeam 9 e versões posteriores
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
![diagrama de camadas do StorSimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

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

| Capacidade de armazenamento | 8100 | 8600 |
|---|---|---|
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

![StorSimple como um diagrama lógico de destino de cópia de segurança primário](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

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

![StorSimple como um diagrama lógico de destino de cópia de segurança secundário](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

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
3. Implemente o Veeam.

Cada passo é abordado em detalhe nas secções seguintes.

### <a name="set-up-the-network"></a>Configurar a rede

Uma vez que o StorSimple é uma solução que está integrada com a cloud do Azure, StorSimple requer um Active Directory e a ligação de trabalho na cloud do Azure. Esta ligação é utilizada para operações como instantâneos de cloud, gestão de dados e metadados de transferência e a camada mais antiga, menos dados acedidos para armazenamento na cloud do Azure.

Para a solução para desempenho ideal, recomendamos que siga estas práticas recomendadas de rede:

-   A ligação que liga o seu StorSimple disposição em camadas para o Azure tem de cumprir os requisitos de largura de banda. Conseguir isto ao aplicar o nível necessário de Quality of Service (QoS) para a infraestrutura de SLAs de objetivo (RTO) de tempo de comutadores de acordo com seus RPO e a recuperação.
-   Latências de acesso de armazenamento de Blobs do Azure máximas devem ser cerca de 80 ms.

### <a name="deploy-storsimple"></a>Implementar o StorSimple

Para orientações de implementação do StorSimple passo a passo, consulte [implementar no seu dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Implementar o Veeam

Para Veeam melhores práticas de instalação, consulte [Veeam cópia de segurança e as melhores práticas de replicação](https://bp.veeam.expert/), e ler o guia de utilizador em [o Centro de ajuda de Veeam (documentação técnica)](https://www.veeam.com/documentation-guides-datasheets.html).

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

- Não utilize volumes expandidos (criados pelo gerenciamento de disco do Windows). Volumes expandidos não são suportados.
- Formate os volumes com NTFS com o tamanho de unidade de alocação de 64 KB.
- Os volumes do StorSimple são mapeados diretamente para o servidor de Veeam.
    - Utilize iSCSI para servidores físicos.


## <a name="best-practices-for-storsimple-and-veeam"></a>Melhores práticas para o StorSimple e o Veeam

Configurar a solução, de acordo com as diretrizes a seguir algumas secções.

### <a name="operating-system-best-practices"></a>Práticas recomendadas do sistema operativo

- Desative a encriptação do Windows Server e a eliminação de duplicados para o sistema de ficheiros NTFS.
- Desative a desfragmentação do Windows Server nos volumes do StorSimple.
- Desative a indexação do Windows Server nos volumes do StorSimple.
- Execute uma verificação antivírus o anfitrião de origem (não em relação os volumes do StorSimple).
- Desativar a predefinição [manutenção do Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) no Gerenciador de tarefas. Fazer isso em uma das seguintes formas:
  - Desative o configurator de manutenção no agendador de tarefas do Windows.
  - Baixe [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) do Windows Sysinternals. Depois de transferir o PsExec, execute o Windows PowerShell como um administrador e escreva:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Práticas recomendadas do StorSimple

-   Certifique-se de que o dispositivo StorSimple está atualizado para [atualização 3 ou posterior](storsimple-install-update-3.md).
-   Isolar tráfego iSCSI e na cloud. Utilização de ligações de iSCSI dedicado para o tráfego entre o StorSimple e o servidor de cópia de segurança.
-   Certifique-se de que o dispositivo StorSimple é um destino de cópia de segurança dedicado. Cargas de trabalho mistas não são suportadas porque elas afetam o RTO e RPO.

### <a name="veeam-best-practices"></a>Veeam melhores práticas

-   A base de dados do Veeam deve ser local no servidor e reside num volume StorSimple.
-   Para a recuperação após desastre, criar cópias de segurança da base de dados do Veeam num volume do StorSimple.
-   Suportamos cópias de segurança de Veeam, completas e incrementais para esta solução. Recomendamos que não utilize cópias de segurança diferenciais e sintéticas.
-   Ficheiros de dados de cópia de segurança devem conter apenas os dados de uma tarefa específica. Por exemplo, nenhum suporte de dados acrescenta entre as diferentes tarefas são permitidas.
-   Desative a verificação de tarefa. Se necessário, a verificação deve ser agendada após a tarefa de cópia de segurança mais recente. É importante compreender que esta tarefa afeta sua janela de backup.
-   Ative a pré-alocação de suporte de dados.
-   Certifique-se de processamento paralelo está ativado.
-   Desative a compressão.
-   Desative a eliminação de duplicados na tarefa de cópia de segurança.
-   Defina a otimização para **LAN destino**.
-   Ative **criar cópia de segurança Active Directory completa** (a cada 2 semanas).
-   No repositório de cópia de segurança, configurar as **utilizar ficheiros de cópia de segurança por VM**.
-   Definir **utilizar vários fluxos de carregamento por tarefa** ao **8** (é permitido um máximo de 16). Ajuste este número ou reduzir verticalmente com base na utilização da CPU no dispositivo StorSimple.

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

## <a name="set-up-veeam-storage"></a>Configurar o armazenamento de Veeam

### <a name="to-set-up-veeam-storage"></a>Para configurar o armazenamento de Veeam

1.  Na consola de cópia de segurança do Veeam e replicação, no **ferramentas de repositório**, aceda à **infraestrutura de cópia de segurança**. Com o botão direito **repositórios de cópia de segurança**e, em seguida, selecione **adicionar repositório de cópia de segurança**.

    ![Console de gerenciamento de Veeam, página de repositório de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  Na **novo repositório de cópia de segurança** caixa de diálogo, introduza um nome e descrição para o repositório. Selecione **Seguinte**.

    ![Página de consola, o nome e descrição de gerenciamento do Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Para o tipo, selecione **servidor do Microsoft Windows**. Selecione o servidor de Veeam. Selecione **Seguinte**.

    ![Console de gerenciamento de Veeam, selecione tipo de repositório de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Para especificar **localização**, procure e selecione o volume. Selecione o **limitar o máximas tarefas em simultâneo:** caixa de verificação e defina o valor como **4**. Isto garante que apenas quatro discos virtuais estão a ser processados em simultâneo enquanto cada máquina virtual (VM) é processada. Selecione o **avançadas** botão.

    ![Console de gerenciamento de Veeam, selecione volume](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  Na **definições de compatibilidade de armazenamento** caixa de diálogo, selecione a **utilizar ficheiros de cópia de segurança por VM** caixa de verificação.

    ![Console de gerenciamento de Veeam, as definições de compatibilidade do armazenamento](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  Na **novo repositório de cópia de segurança** caixa de diálogo, selecione a **ativar o serviço NFS vPower no servidor de montagem (recomendado)** caixa de verificação. Selecione **Seguinte**.

    ![Console de gerenciamento de Veeam, página de repositório de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Reveja as definições e, em seguida, selecione **seguinte**.

    ![Console de gerenciamento de Veeam, página de repositório de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Um repositório é adicionado ao servidor Veeam.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como um destino de cópia de segurança primário

> [!IMPORTANT]
> Restauro de dados a partir de uma cópia de segurança que tem sido em camadas para a cloud ocorre em velocidades de cloud.

A figura seguinte mostra o mapeamento de um volume normal para uma tarefa de cópia de segurança. Neste caso, todas as cópias de segurança semanais mapeiam para o disco completo de Sábado e as cópias de segurança incrementais mapeiam para discos de incrementais de segunda-feira a sexta-feira. Todos os backups e restaurações são de um StorSimple camadas volume.

![Diagrama lógico de configuração de destino de cópia de segurança primário](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple como um destino de cópia de segurança primário GFS agendar exemplo

Eis um exemplo de uma agenda de rotação GFS para quatro semanas, mensais e anuais:

| Tipo de frequência/cópia de segurança | Completo | Incremental (1 a 5 de dias)  |   
|---|---|---|
| Semanal (1 a 4 de semanas) | Sábado | De segunda a sexta-feira |
| Mensalmente  | Sábado  |   |
| Anualmente | Sábado  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Atribuir volumes do StorSimple para uma tarefa de cópia de segurança Veeam

Para o cenário de destino de cópia de segurança primário, crie uma tarefa diária com o volume do Veeam StorSimple primário. Para um cenário de destino de cópia de segurança secundário, crie uma tarefa diária com armazenamento anexado direto (DAS), armazenamento de anexado de rede (NAS) ou apenas um armazenamento de monte de discos (JBOD).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Para atribuir volumes do StorSimple para uma tarefa de cópia de segurança Veeam

1.  Na consola do Veeam cópia de segurança e replicação, selecione **cópia de segurança e replicação**. Com o botão direito **cópia de segurança**e, em seguida, selecione **VMware** ou **Hyper-V**, dependendo do seu ambiente.

    ![Console de gerenciamento de Veeam, nova tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  Na **nova tarefa de cópia de segurança** caixa de diálogo, introduza um nome e descrição para a tarefa de cópia de segurança diária.

    ![Console de gerenciamento de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Selecione uma máquina virtual para voltar ao.

    ![Console de gerenciamento de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Selecione os valores que pretende para **proxy de cópia de segurança** e **repositório de cópia de segurança**. Selecione um valor para **restaurar pontos para manter no disco** , de acordo com as definições de RPO e RTO para o seu ambiente no armazenamento ligado localmente. Selecione **Avançadas**.

    ![Console de gerenciamento de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. Na **definições avançadas** caixa de diálogo a **cópia de segurança** separador, selecione **Incremental**. Certifique-se de que o **criar cópias de segurança completas sintéticas periodicamente** caixa de verificação está desmarcada. Selecione o **criar cópias de segurança completas active periodicamente** caixa de verificação. Sob **Active Directory cópia de segurança completa**, selecione a **semanalmente em dias selecionados** caixa de verificação de Sábado.

    ![Console de gerenciamento de Veeam, a nova tarefa de cópia de segurança avançada página de definições](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Sobre o **armazenamento** separador, certifique-se de que o **ativar a eliminação de dados duplicados inline** caixa de verificação está desmarcada. Selecione o **blocos de ficheiros de troca de exclusão** caixa de verificação e selecione o **excluir eliminar blocos de arquivo** caixa de verificação. Definir **nível de compactação** ao **nenhum**. Com balanceamento de desempenho e a eliminação de duplicados, defina **otimização de armazenamento** ao **destino de LAN**. Selecione **OK**.

    ![Console de gerenciamento de Veeam, a nova tarefa de cópia de segurança avançada página de definições](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Para obter informações sobre as definições de eliminação de duplicados e a compressão da Veeam, consulte [compressão de dados e eliminação de duplicados](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  Na **Editar tarefa de cópia de segurança** caixa de diálogo, pode selecionar o **ativar processamento com suporte a aplicativos** caixa de verificação (opcional).

    ![Console de gerenciamento de Veeam, nova página de processamento de convidado de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Defina a agenda para executar uma vez por dia, ao mesmo tempo, pode especificar.

    ![Console de gerenciamento de Veeam, nova página de agenda de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como um destino de cópia de segurança secundário

> [!NOTE]
> Restauros de dados de uma cópia de segurança que tem sido em camadas para a cloud ocorrerem em velocidades de cloud.

Nesse modelo, tem de ter um suporte de dados de armazenamento (que não seja o StorSimple) para ser usado como um cache temporário. Por exemplo, pode utilizar uma matriz redundante de volume de discos independentes (RAID) para acomodar o espaço, entrada/saída (e/s) e largura de banda. Recomendamos que utilize o RAID 5, 50 e 10.

A figura a seguir mostra típico volumes de locais (para o servidor) de retenção curto prazo e volumes de arquivo de retenção longo prazo. Neste cenário, executam todas as cópias de segurança no volume RAID local (para o servidor). Estas cópias de segurança são periodicamente duplicadas e arquivadas para um volume de arquivo. É importante para o tamanho do volume RAID local (para o servidor), para que ele pode lidar com os requisitos de capacidade e desempenho de retenção curto prazo.

![StorSimple como diagrama lógico de destino de cópia de segurança secundário](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo GFS do destino de cópia de segurança secundário

A tabela seguinte mostra como configurar as cópias de segurança para executar no local e discos de StorSimple. Ele inclui os requisitos de capacidade total e individuais.

| Tipo de cópia de segurança e retenção | Armazenamento configurado | Tamanho (TiB) | Multiplicador GFS | Capacidade total\* (TiB) |
|---|---|---|---|---|
| Semana 1 (completas e incrementais) |Disco local (de curta duração)| 1 | 1 | 1 |
| 2 a 4 do StorSimple semanas |Disco do StorSimple (longo prazo) | 1 | 4 | 4 |
| Total mensal |Disco do StorSimple (longo prazo) | 1 | 12 | 12 |
| Total anual |Disco do StorSimple (longo prazo) | 1 | 1 | 1 |
|Requisito de tamanho de volumes GFS |  |  |  | 18*|

\* Capacidade total inclui 17 TiB de StorSimple discos e de 1 TiB de volume RAID local.


### <a name="gfs-example-schedule"></a>Agenda de exemplo GFS

Rotação de GFS agenda semanal, mensal e anual

| Week (Semana) | Completo | Incremental dia 1 | Incremental dia 2 | Incremental dia 3 | Dia incremental 4 | Dia incremental 5 |
|---|---|---|---|---|---|---|
| Semana 1 | Volume RAID local  | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local |
| Semana de 2 | 2 a 4 do StorSimple semanas |   |   |   |   |   |
| Semana de 3 | 2 a 4 do StorSimple semanas |   |   |   |   |   |
| Semana de 4 | 2 a 4 do StorSimple semanas |   |   |   |   |   |
| Mensalmente | StorSimple mensalmente |   |   |   |   |   |
| Anualmente | StorSimple anuais  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Atribuir volumes do StorSimple para uma tarefa de cópia de Veeam

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Para atribuir volumes do StorSimple para uma tarefa de cópia de Veeam

1.  Na consola do Veeam cópia de segurança e replicação, selecione **cópia de segurança e replicação**. Com o botão direito **cópia de segurança**e, em seguida, selecione **VMware** ou **Hyper-V**, dependendo do seu ambiente.

    ![Console de gerenciamento de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  Na **nova tarefa de cópia da cópia de segurança** caixa de diálogo, introduza um nome e descrição para a tarefa.

    ![Console de gerenciamento de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Selecione as VMs que pretende processar. Selecionar a partir de cópias de segurança e, em seguida, selecione a cópia de segurança diária que criou anteriormente.

    ![Console de gerenciamento de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Exclua objetos provenientes da tarefa de cópia de segurança, se necessário.

5.  Selecione o seu repositório de cópia de segurança e definir um valor para **restaurar pontos a ter**. Certifique-se de que selecione os **tenha os seguintes pontos de restauração para fins de arquivamento** caixa de verificação. Definir a frequência de cópia de segurança e, em seguida, selecione **avançadas**.

    ![Console de gerenciamento de Veeam, nova página de tarefa de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Especifique as seguintes definições avançadas:

    * Sobre o **manutenção** separador, desativar a proteção de Corrupção de nível de armazenamento.

    ![Console de gerenciamento de Veeam, a nova tarefa de cópia de segurança avançada página de definições](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Sobre o **armazenamento** separador, certifique-se de que a eliminação de duplicados e compressão estão desativadas.

    ![Console de gerenciamento de Veeam, a nova tarefa de cópia de segurança avançada página de definições](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Especifique que a transferência de dados é direta.

8.  Definir a agenda da janela de cópia de segurança, de acordo com suas necessidades e, em seguida, conclua o assistente.

Para obter mais informações, consulte [criar tarefas de cópia de segurança](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de cloud do StorSimple

Instantâneos de cloud do StorSimple proteger os dados que residem no seu dispositivo StorSimple. A criação de um instantâneo de cloud é equivalente a bandas de cópia de segurança locais para um recurso de fora do local de envio. Se utilizar o armazenamento com redundância geográfica do Azure, criar um instantâneo de cloud é equivalente ao envio de bandas de cópia de segurança para vários sites. Se tiver de restaurar um dispositivo após um desastre, pode colocar outro dispositivo com StorSimple online e fazer uma ativação pós-falha. Após a ativação pós-falha, seria capaz de acessar os dados (em velocidades de nuvem) a partir do instantâneo de cloud mais recente.

A seguinte secção descreve como criar um script curto para iniciar e eliminar instantâneos de cloud do StorSimple durante o pós-processamento de cópia de segurança.

> [!NOTE]
> Instantâneos que são criados manualmente ou através de programação não siga a política de expiração de instantâneos do StorSimple. Estes instantâneos têm de ser manual ou programaticamente eliminados.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e eliminar instantâneos de cloud com um script

> [!NOTE]
> Avalie cuidadosamente o repercussões de retenção de dados e de conformidade antes de eliminar um instantâneo do StorSimple. Para obter mais informações sobre como executar um script pós-cópia de segurança, consulte a documentação de Veeam.


### <a name="backup-lifecycle"></a>Ciclo de vida de cópia de segurança

![Diagrama de ciclo de vida de cópia de segurança](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

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
4. Para adicionar o script para a sua tarefa de cópia de segurança, edite o seu trabalho Veeam opções avançadas.

    ![Guia de scripts de cópia de segurança de configurações avançadas de Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Recomendamos que execute a sua política de cópia de segurança de instantâneos do StorSimple cloud como um script pós-processamento no final da sua tarefa de cópia de segurança diária. Para obter mais informações sobre como criar cópias de segurança e restaurar o ambiente de aplicativo de backup para o ajudar a cumprir o RPO e RTO, consulte com o arquiteto de cópia de segurança.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como uma origem de restauro

Restaura a partir de um trabalho de dispositivos do StorSimple como restaura a partir de qualquer dispositivo de armazenamento de blocos. As restaurações de dados que estão em camadas para a cloud ocorre em velocidades de cloud. Para dados locais, restaurações ocorrerem com a rapidez de disco local do dispositivo.

Com Veeam, obtém recuperação rápida, granular, ao nível do ficheiro através do StorSimple através de vistas explorer incorporado na consola do Veeam. Utilize os gerenciadores de Veeam para recuperar itens individuais, como mensagens de e-mail, objetos do Active Directory e itens do SharePoint a partir de cópias de segurança. A recuperação pode ser feita sem interrupção de VM no local. Também pode efetuar a recuperação de ponto no tempo para bases de dados Azure SQL Database e Oracle. Veeam e no StorSimple tornam o processo de recuperação ao nível do item do Azure rápida e fáceis. Para obter informações sobre como executar um restauro, consulte a documentação de Veeam:

- Para [o Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Para [do Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Para [do SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Para [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Para [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>Recuperação após desastre e de ativação pós-falha do StorSimple

> [!NOTE]
> Para cenários de destino de cópia de segurança, o StorSimple Cloud Appliance não é suportado como um destino de restauro.

Após um desastre pode ser causado por uma variedade de fatores. A tabela seguinte lista os cenários comuns de recuperação após desastre.

| Cenário | Impacto | Como recuperar | Notas |
|---|---|---|---|
| Falha de dispositivo do StorSimple | Operações de backup e restauração são interrompidas. | Substitua o dispositivo com falhas e realizar [StorSimple ativação pós-falha e recuperação após desastre](storsimple-device-failover-disaster-recovery.md). | Se precisar de executar uma restauração após a recuperação de dispositivo, os conjuntos de trabalho de dados completos são obtidos a partir da cloud para o novo dispositivo. Todas as operações são em velocidades de cloud. O índice e o catálogo reanálise processo podem fazer com que todos os conjuntos de cópia de segurança a serem analisados e obtido a partir da camada de cloud para o escalão de dispositivo local, que pode ser um processo demorado. |
| Falha de servidor Veeam | Operações de backup e restauração são interrompidas. | Reconstrua o servidor de cópia de segurança e efetuar o restauro de base de dados, conforme detalhado no [Centro de ajuda do Veeam (documentação técnica)](https://www.veeam.com/documentation-guides-datasheets.html).  | Tem de reconstruir ou restaurar o servidor de Veeam no site de recuperação após desastre. Restaure a base de dados para o ponto mais recente. Se a base de dados restaurada do Veeam não está sincronizada com as tarefas de cópia de segurança mais recente, indexação e catalogação são necessário. Este índice e o catálogo de reanálise processo podem fazer com que todos os conjuntos de cópia de segurança a serem analisados e obtido a partir da camada de cloud para a camada do dispositivo local. Isto torna ainda mais tempo intensiva. |
| Falha do site que resulta na perda do servidor de cópia de segurança e o StorSimple | Operações de backup e restauração são interrompidas. | Restaurar o StorSimple primeiro e, em seguida, restaure Veeam. | Restaurar o StorSimple primeiro e, em seguida, restaure Veeam. Se precisar de executar uma restauração após a recuperação de dispositivo, conjuntos de dados completa de trabalho são obtidos a partir da cloud para o novo dispositivo. Todas as operações são em velocidades de cloud. |


## <a name="references"></a>Referências

Os seguintes documentos foram referenciados neste artigo:

- [Configuração do StorSimple multipath e/s](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: Aprovisionamento dinâmico](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Utilizar o GPT unidades](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias de sombra para pastas partilhadas](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como [restauro a partir de um conjunto de cópias de segurança](storsimple-restore-from-backup-set-u2.md).
- Saiba mais sobre como efetuar [dispositivo ativação pós-falha e recuperação após desastre](storsimple-device-failover-disaster-recovery.md).
