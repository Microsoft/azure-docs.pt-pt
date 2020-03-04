---
title: Back up Máquinas virtuais Hyper-V com MABS
description: Este artigo contém os procedimentos para o backup e recuperação de máquinas virtuais utilizando o Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: 00d1dd04522c51e4d68450a7b8f25d7159d63724
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255054"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Backup máquinas virtuais Hyper-V com servidor de backup Azure

Este artigo explica como fazer backup de máquinas virtuais Hyper-V utilizando o Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Cenários suportados

O MABS pode fazer o back-up de máquinas virtuais em servidores de anfitriões Hyper-V nos seguintes cenários:

- **Máquinas virtuais com armazenamento local ou direto** - Máquinas virtuais de back up hospedadas em servidores autónomos de anfitriões Hyper-V que têm armazenamento local ou diretamente anexado. Por exemplo: um disco rígido, um dispositivo de rede de área de armazenamento (SAN) ou um dispositivo de armazenamento ligado à rede (NAS). O agente de proteção MABS deve ser instalado em todos os anfitriões.

- **Máquinas virtuais num cluster com armazenamento CSV** - Máquinas virtuais de back up hospedadas num cluster Hyper-V com armazenamento cluster shared volume (CSV). O agente de proteção MABS está instalado em cada nó de cluster.

## <a name="host-versus-guest-backup"></a>Anfitrião contra reserva de hóspedes

O MABS pode fazer um backup de hiper-V de hospeda-v. Ao nível do hospedeiro, o agente de proteção MABS está instalado no servidor ou cluster de anfitriões Hyper-V e protege todos os VMs e ficheiros de dados que estão a ser recorridos nesse hospedeiro.   Ao nível do hóspede, o agente é instalado em cada máquina virtual e protege a carga de trabalho presente nessa máquina.

Ambos os métodos têm prós e contras:

- As cópias de segurança ao nível do hospedeiro são flexíveis porque funcionam independentemente do tipo de SISTEMA em funcionamento nas máquinas de hóspedes e não requerem a instalação do agente de proteção MABS em cada VM. Se implementar a cópia de segurança do nível do anfitrião, pode recuperar uma máquina virtual inteira, ou ficheiros e pastas (recuperação ao nível do item).

- A cópia de segurança ao nível do hóspede é útil se quiser proteger cargas de trabalho específicas em funcionamento numa máquina virtual. Ao nível do anfitrião pode recuperar um VM inteiro ou ficheiros específicos, mas não fornecerá recuperação no contexto de uma aplicação específica. Por exemplo, para recuperar itens específicos do SharePoint de um VM apoiado, deve fazer backup ao nível dos hóspedes desse VM. Utilize a cópia de segurança ao nível do hóspede se pretender proteger os dados armazenados nos discos de passagem. A passthrough permite que a máquina virtual aceda diretamente ao dispositivo de armazenamento e não armazena dados de volume virtual num ficheiro VHD.

## <a name="how-the-backup-process-works"></a>Como funciona o processo de backup

O MABS executa backup com VSS da seguinte forma. Os passos nesta descrição são numerados para ajudar com clareza.

1. O motor de sincronização baseado em bloco MABS faz uma cópia inicial da máquina virtual protegida e garante que a cópia da máquina virtual é completa e consistente.

2. Depois de a cópia inicial ser feita e verificada, o MABS utiliza o escritor Hyper-V VSS para capturar cópias de segurança. O escritor VSS fornece um conjunto consistente de dados de blocos de disco que são sincronizados com o servidor MABS. Esta abordagem proporciona o benefício de uma "cópia de segurança completa" com o servidor MABS, minimizando ao mesmo tempo os dados de backup que devem ser transferidos através da rede.

3. O agente de proteção MABS num servidor que está a executar o Hyper-V utiliza as APIs hiper-V existentes para determinar se uma máquina virtual protegida também suporta VSS.

   - Se uma máquina virtual cumprir os requisitos para a cópia de segurança on-line e tiver instalado o componente de serviços de integração Hyper-V, então o escritor Hyper-V VSS reencaminha recursivamente o pedido vsS através de todos os processos de conhecimento VSS na máquina virtual. Esta operação ocorre sem que o agente de proteção MABS seja instalado na máquina virtual. O pedido VSS recursivo permite ao escritor Hyper-V VSS garantir que as operações de escrita de disco são sincronizadas de modo a que um instantâneo VSS seja capturado sem a perda de dados.

     A componente de serviços de integração Hyper-V invoca o escritor Hyper-V VSS em Volume Shadow Copy Services (VSS) em máquinas virtuais para garantir que os seus dados de aplicação estão num estado consistente.

   - Se a máquina virtual não cumprir os requisitos de backup on-line, o MABS utiliza automaticamente as APIs Hyper-V para parar a máquina virtual antes de capturar ficheiros de dados.

4. Após a cópia inicial da máquina virtual sincronizar com o servidor MABS, todas as alterações que são feitas aos recursos da máquina virtual são capturadas num novo ponto de recuperação. O ponto de recuperação representa o estado consistente da máquina virtual num momento específico. As capturas de pontos de recuperação podem ocorrer pelo menos uma vez por dia. Quando um novo ponto de recuperação é criado, o MABS usa replicação de nível de bloco em conjunto com o escritor Hyper-V VSS para determinar quais os blocos que foram alterados no servidor que está a executar Hyper-V após a criação do último ponto de recuperação. Estes blocos de dados são então transferidos para o servidor MABS e são aplicados à réplica dos dados protegidos.

5. O servidor MABS utiliza VSS nos volumes que acolhem dados de recuperação para que várias cópias-sombra estejam disponíveis. Cada uma destas cópias sombra proporciona uma recuperação separada. Os pontos de recuperação VSS são armazenados no servidor MABS. A cópia temporária que é feita no servidor em hiper-V, é armazenada apenas durante a duração da sincronização MABS.

>[!NOTE]
>
>A partir do Windows Server 2016, os discos rígidos virtuais Hyper-V têm um rastreio de mudança incorporado conhecido como rastreio de mudança resiliente (RCT). O MABS utiliza RCT (o rastreio de mudança nativa em Hyper-V), o que diminui a necessidade de verificações de consistência morosas em cenários como acidentes de VM. O RCT proporciona uma melhor resiliência do que o rastreio de alterações fornecido por cópias de segurança baseadas em instantâneos VSS. O MABS V3 otimiza ainda mais o consumo de rede e armazenamento, transferindo apenas os dados alterados durante quaisquer verificações de consistência.

## <a name="backup-prerequisites"></a>Pré-requisitos de backup

Estes são os pré-requisitos para apoiar máquinas virtuais Hyper-V com MABS:

|Pré-requisito|Detalhes|
|------------|-------|
|Pré-requisitos do MABS|- Se pretender efetuar uma recuperação ao nível do item para máquinas virtuais (recuperar ficheiros, pastas, volumes), terá de instalar a função Hyper-V no servidor MABS.  Se só quer recuperar a máquina virtual e não o nível de item, então o papel não é necessário.<br />- Pode proteger até 800 máquinas virtuais de 100 GB cada um num servidor MABS e permitir vários servidores MABS que suportam clusters maiores.<br />- O MABS exclui o ficheiro da página de cópias de segurança incrementais para melhorar o desempenho da cópia de segurança virtual da máquina.<br />- O MABS pode fazer o apoio a um servidor ou cluster Hyper-V no mesmo domínio que o servidor MABS, ou num domínio infantil ou de confiança. Se quiser apoiar o Hyper-V num grupo de trabalho ou num domínio não confiável, terá de configurar a autenticação. Para um único servidor Hyper-V, pode utilizar a autenticação NTLM ou certificado. Para um cluster, pode utilizar apenas a autenticação do certificado.<br />- Não é suportado o uso de cópias de segurança de nível de anfitrião para fazer cópias de segurança dos dados das máquinas virtuais nos discos de passagem. Neste cenário, recomendamos que utilize cópias de segurança de nível de anfitrião para fazer backup de ficheiros VHD e backup ao nível do hóspede para fazer backup os outros dados que não são visíveis no hospedeiro.<br />   \- Pode fazer cópias de venda de VMs armazenadas em volumes duplicados.|
|Pré-requisitos de VM hiper-V|- A versão dos Componentes de Integração que está a funcionar na máquina virtual deve ser a mesma que a versão do hospedeiro Hyper-V. <br />- Para cada cópia de segurança da máquina virtual, necessitará de espaço livre no volume que acolhe os ficheiros de disco rígido virtual para permitir espaço em Hiper-V para disferencing de discos (AVHD's) durante a cópia de segurança. O espaço deve ser pelo menos igual ao cálculo Tamanho inicial do disco\*taxa de churn\*tempo de janela **de backup.** Se estiver a executar várias cópias de segurança num cluster, precisará de capacidade de armazenamento suficiente para acomodar os AVHDs para cada uma das máquinas virtuais usando este cálculo.<br />- Para fazer o backup de máquinas virtuais localizadas em servidores anfitriões Hyper-V que executam o Windows Server 2012 R2, a máquina virtual deve ter um controlador SCSI especificado, mesmo que não esteja ligado a nada. (Na cópia de segurança on-line do Windows Server 2012 R2, o anfitrião Hyper-V monta um novo VHD no VM e, em seguida, desmonta-o. Apenas o controlador SCSI pode suportar isto e, portanto, é necessário para cópia de segurança on-line da máquina virtual.  Sem esta definição, o ID 10103 do evento será emitido quando tentar fazer o back up da máquina virtual.)|
|Pré-requisitos linux|- Pode apoiar as máquinas virtuais linux utilizando MABS. Apenas são suportadas imagens consistentes com ficheiros.|
|VMs de back up com armazenamento CSV|- Para armazenamento CSV, instale o fornecedor de hardware do Volume Shadow Copy Services (VSS) no servidor Hyper-V. Contacte o fornecedor da sua rede de área de armazenamento (SAN) para o fornecedor de hardware VSS.<br />- Se um único nó se desligar inesperadamente num cluster CSV, o MABS irá efetuar uma verificação de consistência contra as máquinas virtuais que estavam a funcionar naquele nó.<br />- Se necessitar de reiniciar um servidor Hyper-V que tenha encriptação de unidade BitLocker ativada no cluster CSV, tem de fazer uma verificação de consistência para as máquinas virtuais Hyper-V.|
|VMs de back up com armazenamento SMB|- Ligue a montagem automática no servidor que está a executar Hyper-V para ativar a proteção virtual da máquina.<br />   - Desativar a descarga da chaminé TCP.<br />- Certifique-se de que todas as contas de hiper-V machine$ têm permissões completas nas ações específicas de ficheiros SMB remotos.<br />- Certifique-se de que o caminho de ficheiro para todos os componentes da máquina virtual durante a recuperação para uma localização alternativa é inferior a 260 caracteres. Se não, a recuperação pode ter sucesso, mas o Hyper-V não será capaz de montar a máquina virtual.<br />- Os seguintes cenários não são suportados:<br />     Implantações em que alguns componentes da máquina virtual se encontram em volumes locais e alguns componentes estão em volumes remotos; um endereço IPv4 ou IPv6 para servidor de ficheiros de localização de armazenamento e recuperação de uma máquina virtual para um computador que utiliza ações SMB remotas.<br />- Terá de ativar o serviço de agente VSS do Servidor de Ficheiros em cada servidor SMB - Adicione-o em **funções de Adicionar e funcionalidades** > **Selecione funções** de servidor > Serviços de **Ficheiros e Serviços** de Armazenamento > **Serviços** de **Ficheiros** > Serviço de Ficheiros > **Serviço de Agente VSS**do Servidor de Ficheiros .|

## <a name="back-up-virtual-machines"></a>Máquinas virtuais de back up

1. Instale o seu [servidor MABS](backup-azure-microsoft-azure-backup.md) e [o seu armazenamento.](backup-mabs-add-storage.md) Ao configurar o seu armazenamento, utilize estas diretrizes de capacidade de armazenamento.
   - Tamanho médio da máquina virtual - 100 GB
   - Número de máquinas virtuais por servidor MABS - 800
   - Tamanho total de 800 VMs - 80 TB
   - Espaço necessário para armazenamento de backup - 80 TB

2. Instale o agente de proteção MABS no servidor Hyper-V ou nos nós de cluster Hyper-V. Se estiver a fazer backup ao nível dos hóspedes, vai instalar o agente nos VMs que pretende fazer de reserva ao nível dos hóspedes.

3. Na consola do Administrador MABS, clique em **Proteger** > **Criar grupo de proteção** para abrir o assistente **do Create New Protection Group.**

4. Na página **Select Group Members,** selecione os VMs que pretende proteger dos servidores de anfitriões Hyper-V em que estão localizados. Recomendamos que coloque todos os VMs que terão a mesma política de proteção num só grupo de proteção. Para fazer uma utilização eficiente do espaço, permita a colocalização. A colocalização permite localizar dados de diferentes grupos de proteção no mesmo depósito de disco ou fita, de modo que várias fontes de dados têm uma única réplica e volume de ponto de recuperação.

5. Na página **Select Data Protection Method,** especifique um nome de grupo de proteção. Selecione **quero proteção de curto prazo usando o Disco** e selecione quero **proteção on-line** se quiser fazer cópias de segurança para o Azure utilizando o serviço de backup Azure.

6. Em **Especificar Objetivos de Curto Prazo** > intervalo de **retenção,** especifique quanto tempo pretende reter os dados do disco. Na **frequência de sincronização,** especifique quantas cópias incrementais dos dados devem ser executadas. Alternativamente, em vez de selecionar um intervalo para backups incrementais, pode ativar antes de um ponto de **recuperação**. Com esta definição ativada, o MABS executará um backup completo expresso imediatamente antes de cada ponto de recuperação programado.

    > [!NOTE]
    >
    >Se estiver a proteger as cargas de trabalho da aplicação, os pontos de recuperação são criados de acordo com a frequência de Sincronização, desde que a aplicação suporte backups incrementais. Se não o fizer, então a MABS executa uma cópia de segurança expressa, em vez de uma cópia de segurança incremental, e cria pontos de recuperação de acordo com o calendário de backup expresso.

7. Na página de atribuição de **discos de revisão,** reveja o espaço de disco de armazenamento atribuído ao grupo de proteção.

   **O tamanho total de Dados** é o tamanho dos dados que pretende fazer cópia sinuoso, e o espaço do Disco a fornecer no **MABS** é o espaço que o MABS recomenda para o grupo de proteção. O MABS escolhe o volume de backup ideal, com base nas definições. No entanto, pode editar as escolhas de volume de cópia de segurança nos detalhes da atribuição do **Disco.** Para as cargas de trabalho, selecione o armazenamento preferido no menu dropdown. As suas edidas alteram os valores para **armazenamento total** e **armazenamento gratuito** no painel de armazenamento de **discos disponível.** Espaço subprovisionado é a quantidade de armazenamento MABS sugere que adicione ao volume, para continuar com backups suavemente no futuro.

8. Na página Método de Criação de **Réplicas Escolha,** especifique como será realizada a replicação inicial de dados no grupo de proteção. Se selecionar para **replicar automaticamente sobre a rede,** recomendamos que escolha um tempo fora do pico. Para grandes quantidades de dados ou menos do que as condições ideais de rede, considere selecionar **Manualmente**, o que requer replicar os dados offline utilizando meios amovíveis.

9. Na página **'Verificação de Consistência',** selecione como pretende automatizar verificações de consistência. Só é possível permitir que um cheque seja executado quando os dados da réplica se tornarem inconsistentes, ou de acordo com um horário. Se não pretender configurar a verificação automática de consistência, pode fazer uma verificação manual a qualquer momento clicando no grupo de proteção e selecionando a **Verificação**de Consistência de Executar .

    Depois de criar o grupo de proteção, a replicação inicial dos dados ocorre de acordo com o método selecionado. Após a replicação inicial, cada cópia de segurança ocorre de acordo com as definições do grupo de proteção. Se necessitar de recuperar os dados com backup, note o seguinte:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Máquinas virtuais de back up configuradas para migração ao vivo

Quando as máquinas virtuais estão envolvidas na migração ao vivo, o MABS continua a proteger as máquinas virtuais enquanto o agente de proteção MABS for instalado no hospedeiro Hyper-V. A forma como o MABS protege as máquinas virtuais depende do tipo de migração viva envolvida.

**Migração viva dentro** de um cluster - Quando uma máquina virtual é migrada dentro de um cluster MABS deteta a migração, e apoia a máquina virtual a partir do novo nó de cluster sem qualquer requisito de intervenção do utilizador. Como o local de armazenamento não mudou, o MABS continua com cópias de segurança expressas.

**Migração ao vivo fora do cluster** - Quando uma máquina virtual é migrada entre servidores autónomos, diferentes clusters, ou entre um servidor autónomo e um cluster, o MABS deteta a migração e pode apoiar a máquina virtual sem a intervenção do utilizador.

### <a name="requirements-for-maintaining-protection"></a>Requisitos para manter a proteção

Seguem-se os requisitos para manter a proteção durante a migração ao vivo:

- Os anfitriões Hyper-V para as máquinas virtuais devem estar localizados numa nuvem VMM do System Center, num servidor VMM, funcionando pelo menos o System Center 2012 com SP1.

- O agente de proteção MABS deve ser instalado em todos os anfitriões hyper-V.

- Os servidores MABS devem ser ligados ao servidor VMM. Todos os servidores de anfitriões Hyper-V na nuvem VMM também devem estar ligados aos servidores MABS. Isto permite que o MABS se comunique com o servidor VMM para que o MABS possa descobrir em que servidor de anfitriões Hyper-V a máquina virtual está atualmente a funcionar, e criar uma nova cópia de segurança desse servidor Hyper-V. Se uma ligação não puder ser estabelecida no servidor Hyper-V, a cópia de segurança falha com uma mensagem de que o agente de proteção MABS está incontactável.

- Todos os servidores MABS, servidores VMM e servidores de anfitriões Hyper-V devem estar no mesmo domínio.

### <a name="details-about-live-migration"></a>Detalhes sobre migração ao vivo

Note o seguinte para backup durante a migração ao vivo:

- Se uma migração ao vivo transfere o armazenamento, o MABS realiza uma verificação de consistência total da máquina virtual, e depois continua com cópias de segurança expressas. Quando ocorre uma migração ao vivo do armazenamento, o Hyper-V reorganiza o disco rígido virtual (VHD) ou VHDX, o que provoca um aumento único no tamanho dos dados de backup DoMABS.

- No hospedeiro da máquina virtual, ligue o montagem automática para permitir a proteção virtual e desative a descarga da chaminé TCP.

- O MABS utiliza a porta 6070 como porta padrão para hospedar o Serviço de Ajuda DPM-VMM. Para alterar o registo:

    1. Navegue para **HKLM\Software\Microsoft\Microsoft Data Protection Manager\Configuração**.
    2. Crie um valor DWORD de 32 bits: DpmVmmHelperServicePort, e escreva o número de porta atualizado como parte da chave de registo.
    3. Abra ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config```e mude o número do porto de 6070 para o novo porto. Por exemplo: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Reinicie o serviço de ajuda DPM-VMM e reinicie o serviço DPM.

### <a name="set-up-protection-for-live-migration"></a>Criar proteção para migração ao vivo

Para criar proteção para a migração viva:

1. Instale o servidor MABS e o seu armazenamento e instale o agente de proteção MABS em todos os servidores ou nódeo cluster Hyper-V na nuvem VMM. Se estiver a utilizar o armazenamento SMB num cluster, instale o agente de proteção MABS em todos os nós do cluster.

2. Instale a consola VMM como componente cliente no servidor MABS para que o MABS possa comunicar com o servidor VMM. A consola deve ser a mesma versão que a que está a funcionar no servidor VMM.

3. Atribuir a conta MABSMachineName$ como uma conta de administrador apenas de leitura no servidor de gestão VMM.

4. Ligue todos os servidores de anfitriões Hyper-V a todos os servidores MABS com o cmdlet `Set-DPMGlobalProperty` PowerShell. O cmdlet aceita vários nomes de servidorEs MABS. Utilize o formato: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Para mais informações, consulte [Set-DPMGlobalProperty](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmglobalproperty?view=systemcenter-ps-2019).

5. Depois de todas as máquinas virtuais que correm nos anfitriões Hyper-V nas nuvens VMM são descobertas em VMM, configuram um grupo de proteção e adicionam as máquinas virtuais que pretende proteger. Devem ser ativados controlos automáticos de coerência ao nível do grupo de proteção para proteção em cenários de mobilidade virtual das máquinas.

6. Depois de configuradas as definições, quando uma máquina virtual migra de um cluster para outro todas as cópias de segurança continuam como esperado. Pode verificar se a migração ao vivo está ativada da seguinte forma:

   1. Verifique se o Serviço de Ajudante DPM-VMM está em funcionamento. Se não for, começa.

   2. Abra o Microsoft SQL Server Management Studio e ligue-se à instância que acolhe a base de dados MABS (DPMDB). No DPMDB execute a seguinte consulta: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Esta consulta contém uma propriedade chamada `KnownVMMServer`. Este valor deve ser o mesmo valor que forneceu com o `Set-DPMGlobalProperty` cmdlet.

   3. Execute a seguinte consulta para validar o parâmetro *VMMIdentifier* na `PhysicalPathXML` para uma determinada máquina virtual. Substitua `VMName` pelo nome da máquina virtual.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Abra o ficheiro .xml que esta consulta retorna e valide que o campo *VMMIdentifier* tem um valor.

### <a name="run-manual-migration"></a>Executar migração manual

Depois de completar os passos nas secções anteriores, e o trabalho do MABS Summary Manager terminar, a migração está ativada. Por defeito, este trabalho começa à meia-noite e corre todas as manhãs. Se quiser fazer uma migração manual, para verificar se está tudo a funcionar como esperado, faça o seguinte:

1. Abra o Estúdio de Gestão de Servidores SQL e ligue-se à instância que acolhe a base de dados MABS.

2. Executar a seguinte consulta: `SELECT SCH.ScheduleId FROM tbl_JM_JobDefinition JD JOIN tbl_SCH_ScheduleDefinition SCH ON JD.JobDefinitionId = SCH.JobDefinitionId WHERE JD.Type = '282faac6-e3cb-4015-8c6d-4276fcca11d4' AND JD.IsDeleted = 0 AND SCH.IsDeleted = 0`. Esta consulta devolve o **ScheduleID**. Note este ID como irá usá-lo no próximo passo.

3. No Estúdio de Gestão de Servidores SQL, expanda o **Agente de Servidores SQL**e, em seguida, expanda **jobs**. Clique no **ScheduleID** que observou e selecione **Iniciar a trabalhar no Passo**.

O desempenho de reserva é afetado quando o trabalho funciona. O tamanho e escala da sua implantação determina o tempo que o trabalho demora a terminar.

## <a name="back-up-replica-virtual-machines"></a>Back up replica máquinas virtuais

Se o MABS estiver a funcionar no Windows Server 2012 R2 ou superior, então pode fazer o back-up de máquinas virtuais de réplica. Isto é útil por várias razões:

**Reduz o impacto das cópias de segurança na carga** de trabalho em funcionamento - Tomar uma cópia de segurança de uma máquina virtual incorre em algumas sobrecargas à medida que um instantâneo é criado. Ao descarregar o processo de backup para um local remoto secundário, a carga de trabalho de funcionamento já não é afetada pela operação de backup. Isto só se aplica a implementações onde a cópia de reserva é armazenada num site remoto. Por exemplo, você pode pegar backups diários e armazenar dados localmente para garantir tempos de restauro rápidos, mas tomar backups mensais ou trimestrais de máquinas virtuais réplicas armazenadas remotamente para retenção a longo prazo.

**Economiza largura** de banda - Numa típica implantação remota de filiais/sedes, você precisa de uma quantidade adequada de largura de banda provisionada para transferir dados de backup entre sites. Se criar uma estratégia de replicação e failover, além da sua estratégia de backup de dados, pode reduzir a quantidade de dados redundantes enviados pela rede. Ao fazer backup dos dados da réplica virtual da máquina em vez do primário, guarda-se a sobrecarga de envio dos dados de backup através da rede.

Ativa a cópia de segurança do **anfitrião** - Pode utilizar um datacenter hospedado como um site de réplica, sem necessidade de um datacenter secundário. Neste caso, o anfitrião SLA requer uma cópia de segurança consistente das máquinas virtuais de réplica.

Uma réplica virtual é desligada até que uma falha seja iniciada, e o VSS não pode garantir uma cópia de segurança consistente para uma réplica virtual. Assim, a cópia de segurança de uma réplica virtual será apenas consistente com o crash. Se a consistência do acidente não puder ser garantida, então o backup falhará e isso pode ocorrer em várias condições:

- A réplica da máquina virtual não é saudável e está em estado crítico.

- A máquina virtual réplica está a resincronizar (na ressincronização em progresso ou ressincronização do estado necessário).

- A replicação inicial entre o local primário e secundário está em andamento ou pendente para a máquina virtual.

- Os registos .hrl estão a ser aplicados à máquina virtual da réplica, ou uma ação anterior para aplicar os troncos .hrl no disco virtual falhou, ou foi cancelado ou interrompido.

- Migração ou failover da réplica máquina virtual está em andamento

## <a name="recover-backed-up-virtual-machines"></a>Recuperar máquinas virtuais apoiadas

Quando conseguir recuperar uma máquina virtual apoiada, utilize o assistente recovery para selecionar a máquina virtual e o ponto de recuperação específico. Para abrir o Assistente de Recuperação e recuperar uma máquina virtual:

1. Na consola do Administrador MABS, escreva o nome do VM ou expanda a lista de itens protegidos e selecione o VM que pretende recuperar.

2. Nos **pontos** de recuperação para o painel, no calendário, clique em qualquer data para ver os pontos de recuperação disponíveis. Em seguida, no painel **Path,** selecione o ponto de recuperação que pretende utilizar no assistente de recuperação.

3. A partir do menu **Ações,** clique em **Recuperar** para abrir o Assistente de Recuperação.

    O VM e o ponto de recuperação selecionados aparecem no ecrã de Seleção de Recuperação de **Revisão.** Clique em **Seguinte**.

4. No ecrã **Select Recovery Type,** selecione onde pretende restaurar os dados e, em seguida, clique **em Seguinte**.

    - **Recuperar para a instância original**: Quando recuperar para a instância original, o VHD original é eliminado. O MABS recupera o VHD e outros ficheiros de configuração para a localização original utilizando o escritor Hyper-V VSS. No final do processo de recuperação, as máquinas virtuais ainda estão altamente disponíveis.
        O grupo de recursos deve estar presente para a recuperação. Se não estiver disponível, recupere para um local alternativo e, em seguida, torne a máquina virtual altamente disponível.

    - **Recupere como máquina virtual para qualquer hospedeiro:** O MABS suporta a recuperação alternativa de localização (ALR), que proporciona uma recuperação perfeita de uma máquina virtual Hiper-V protegida para um diferente hospedeiro Hyper-V, independente da arquitetura do processador. As máquinas virtuais hiper-V que são recuperadas para um nó de cluster não estarão altamente disponíveis. Se escolher esta opção, o Assistente de Recuperação apresenta-lhe um ecrã adicional para identificar o destino e o caminho de destino.

    - **Cópia para uma pasta**de rede : O MABS suporta a recuperação ao nível do item (ILR), que permite fazer a recuperação ao nível do item de ficheiros, pastas, volumes e discos rígidos virtuais (VHDs) de uma cópia de segurança de nível de suporte de máquinas virtuais Hyper-V para uma partilha de rede ou um volume num servidor protegido mABS. O agente de proteção MABS não tem de ser instalado no interior do hóspede para realizar a recuperação do nível do item. Se escolher esta opção, o Assistente de Recuperação apresenta-lhe um ecrã adicional para identificar o destino e o caminho de destino.

5. Em **Especificar Opções** de Recuperação configurar as opções de recuperação e clicar **em Seguinte:**

    - Se estiver a recuperar um VM em baixa largura de banda, clique em **Modificar** para ativar o estrangulamento da largura de **banda da rede**. Depois de ligar a opção de estrangulamento, pode especificar a largura de banda que pretende disponibilizar e o momento em que essa largura de banda está disponível.
    - Selecione **Ativar a recuperação baseada em SAN utilizando imagens** de hardware se tiver configurado a sua rede.
    - Selecione **Enviar um e-mail quando a recuperação estiver concluída** e, em seguida, fornecer os endereços de e-mail, se quiser notificações de e-mail enviadas assim que o processo de recuperação estiver concluído.

6. No ecrã Resumo, certifique-se de que todos os detalhes estão corretos. Se os detalhes não estiverem corretos, ou se quiser fazer uma alteração, clique em **Voltar**. Se estiver satisfeito com as definições, clique em **Recuperar** para iniciar o processo de recuperação.

7. O ecrã **Do Estado de Recuperação** fornece informações sobre o trabalho de recuperação.

## <a name="next-steps"></a>Passos seguintes

[Recuperar dados do Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
