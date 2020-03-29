---
title: Back up Máquinas virtuais Hyper-V com MABS
description: Este artigo contém os procedimentos para o backup e recuperação de máquinas virtuais utilizando o Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: 00d1dd04522c51e4d68450a7b8f25d7159d63724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255054"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Backup máquinas virtuais Hyper-V com servidor de backup Azure

Este artigo explica como fazer backup de máquinas virtuais Hyper-V utilizando o Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Cenários suportados

O MABS pode fazer o back-up de máquinas virtuais em servidores de anfitriões Hyper-V nos seguintes cenários:

- **Máquinas virtuais com armazenamento local ou direto** – crie uma cópia de segurança das máquinas virtuais alojadas em servidores autónomos do Hyper-V que possuam armazenamento local ou ligado diretamente. Por exemplo: um disco rígido, um dispositivo de rede de área de armazenamento (SAN) ou um dispositivo de armazenamento ligado à rede (NAS). O agente de proteção MABS deve ser instalado em todos os anfitriões.

- **Máquinas virtuais num cluster com armazenamento CSV** – crie uma cópia de segurança das máquinas virtuais alojadas num cluster do Hyper-V com o armazenamento Volume Partilhado de Cluster (CSV). O agente de proteção MABS está instalado em cada nó de cluster.

## <a name="host-versus-guest-backup"></a>Anfitrião contra reserva de hóspedes

O MABS pode fazer um backup de hiper-V de hospeda-v. Ao nível do hospedeiro, o agente de proteção MABS está instalado no servidor ou cluster de anfitriões Hyper-V e protege todos os VMs e ficheiros de dados que estão a ser recorridos nesse hospedeiro.   Ao nível do hóspede, o agente é instalado em cada máquina virtual e protege a carga de trabalho presente nessa máquina.

Ambos os métodos têm vantagens e desvantagens:

- As cópias de segurança ao nível do hospedeiro são flexíveis porque funcionam independentemente do tipo de SISTEMA em funcionamento nas máquinas de hóspedes e não requerem a instalação do agente de proteção MABS em cada VM. Se implementar a cópia de segurança do nível do anfitrião, pode recuperar uma máquina virtual inteira, ou ficheiros e pastas (recuperação ao nível do item).

- A cópia de segurança ao nível do hóspede é útil se quiser proteger cargas de trabalho específicas em funcionamento numa máquina virtual. Ao nível do anfitrião pode recuperar um VM inteiro ou ficheiros específicos, mas não fornecerá recuperação no contexto de uma aplicação específica. Por exemplo, para recuperar itens específicos do SharePoint de um VM apoiado, deve fazer backup ao nível dos hóspedes desse VM. Utilize a cópia de segurança ao nível do hóspede se pretender proteger os dados armazenados nos discos de passagem. A passthrough permite que a máquina virtual aceda diretamente ao dispositivo de armazenamento e não armazena dados de volume virtual num ficheiro VHD.

## <a name="how-the-backup-process-works"></a>Como funciona o processo de backup

O MABS executa backup com VSS da seguinte forma. Os passos nesta descrição estão numerados para o ajudar com clareza.

1. O motor de sincronização baseado em bloco MABS faz uma cópia inicial da máquina virtual protegida e garante que a cópia da máquina virtual é completa e consistente.

2. Depois de a cópia inicial ser feita e verificada, o MABS utiliza o escritor Hyper-V VSS para capturar cópias de segurança. O escritor VSS fornece um conjunto consistente de dados de blocos de disco que são sincronizados com o servidor MABS. Esta abordagem proporciona o benefício de uma "cópia de segurança completa" com o servidor MABS, minimizando ao mesmo tempo os dados de backup que devem ser transferidos através da rede.

3. O agente de proteção MABS num servidor que está a executar o Hyper-V utiliza as APIs hiper-V existentes para determinar se uma máquina virtual protegida também suporta VSS.

   - Se uma máquina virtual cumprir os requisitos para uma cópia de segurança online e tiver o componente dos serviços de integração do Hyper-V instalado, o escritor VSS do Hyper-V reencaminha recursivamente o pedido VSS para todos os processos com deteção de VSS na máquina virtual. Esta operação ocorre sem que o agente de proteção MABS seja instalado na máquina virtual. Este pedido do VSS recursivo permite que o escritor VSS do Hyper-V confirme que as operações de escrita no disco estão sincronizadas de forma a capturar o instantâneo do VSS sem ocorrer a perda de dados.

     O componente dos serviços de integração do Hyper-V invoca o escritor de VSS do Hyper-V nos Serviços de Cópia Sombra de Volumes (VSS) nas máquinas virtuais para assegurar que os respetivos dados de aplicação estão num estado consistente.

   - Se a máquina virtual não cumprir os requisitos de backup on-line, o MABS utiliza automaticamente as APIs Hyper-V para parar a máquina virtual antes de capturar ficheiros de dados.

4. Após a cópia inicial da máquina virtual sincronizar com o servidor MABS, todas as alterações que são feitas aos recursos da máquina virtual são capturadas num novo ponto de recuperação. O ponto de recuperação representa o estado consistente da máquina virtual num momento específico. As capturas do ponto de recuperação podem ocorrer, pelo menos, uma vez por dia. Quando um novo ponto de recuperação é criado, o MABS usa replicação de nível de bloco em conjunto com o escritor Hyper-V VSS para determinar quais os blocos que foram alterados no servidor que está a executar Hyper-V após a criação do último ponto de recuperação. Estes blocos de dados são então transferidos para o servidor MABS e são aplicados à réplica dos dados protegidos.

5. O servidor MABS utiliza VSS nos volumes que acolhem dados de recuperação para que várias cópias-sombra estejam disponíveis. Cada uma destas cópias sombra fornece uma recuperação separada. Os pontos de recuperação VSS são armazenados no servidor MABS. A cópia temporária que é feita no servidor em hiper-V, é armazenada apenas durante a duração da sincronização MABS.

>[!NOTE]
>
>A partir do Windows Server 2016, os discos rígidos virtuais Hyper-V têm um rastreio de mudança incorporado conhecido como rastreio de mudança resiliente (RCT). O MABS utiliza RCT (o rastreio de mudança nativa em Hyper-V), o que diminui a necessidade de verificações de consistência morosas em cenários como acidentes de VM. O RCT proporciona uma melhor resiliência do que o controlo de alterações fornecido pelas cópias de segurança baseadas em instantâneos do VSS. O MABS V3 otimiza ainda mais o consumo de rede e armazenamento, transferindo apenas os dados alterados durante quaisquer verificações de consistência.

## <a name="backup-prerequisites"></a>Pré-requisitos da cópia de segurança

Estes são os pré-requisitos para apoiar máquinas virtuais Hyper-V com MABS:

|Pré-requisito|Detalhes|
|------------|-------|
|Pré-requisitos do MABS|- Se pretender efetuar uma recuperação ao nível do item para máquinas virtuais (recuperar ficheiros, pastas, volumes), terá de instalar a função Hyper-V no servidor MABS.  Se só quer recuperar a máquina virtual e não o nível de item, então o papel não é necessário.<br />- Pode proteger até 800 máquinas virtuais de 100 GB cada um num servidor MABS e permitir vários servidores MABS que suportam clusters maiores.<br />- O MABS exclui o ficheiro da página de cópias de segurança incrementais para melhorar o desempenho da cópia de segurança virtual da máquina.<br />- O MABS pode fazer o apoio a um servidor ou cluster Hyper-V no mesmo domínio que o servidor MABS, ou num domínio infantil ou de confiança. Se quiser apoiar o Hyper-V num grupo de trabalho ou num domínio não confiável, terá de configurar a autenticação. Para um único servidor Hyper-V, pode utilizar a autenticação NTLM ou certificado. Para um cluster, pode utilizar apenas a autenticação do certificado.<br />–   Não é suportada a utilização de cópias de segurança ao nível do anfitrião para criar cópias de segurança de máquinas virtuais em discos pass-through. Neste cenário, recomendamos que utilize cópias de segurança de nível de anfitrião para fazer backup de ficheiros VHD e backup ao nível do hóspede para fazer backup os outros dados que não são visíveis no hospedeiro.<br />   - Pode fazer cópias de venda de VMs armazenadas em volumes duplicados.|
|Pré-requisitos de VM do Hyper-V|- A versão dos Componentes de Integração que está a funcionar na máquina virtual deve ser a mesma que a versão do hospedeiro Hyper-V. <br />–   Irá precisar de espaço livre no volume que está a alojar os ficheiros virtuais do disco rígido em cada cópia de segurança das máquinas virtuais para que o Hyper-V tenha espaço suficiente para discos de diferenciação (AVHD) durante a cópia de segurança. O espaço deve ser pelo menos igual ao cálculo **Inicial tamanho\*do disco Churn taxa\*de backup** tempo de janela. Se estiver a executar múltiplas cópias de segurança num cluster, irá precisar de capacidade de armazenamento suficiente para acomodar os ficheiros AVHD nas máquinas virtuais que utilizem este cálculo.<br />- Para fazer o backup de máquinas virtuais localizadas em servidores anfitriões Hyper-V que executam o Windows Server 2012 R2, a máquina virtual deve ter um controlador SCSI especificado, mesmo que não esteja ligado a nada. (Na cópia de segurança on-line do Windows Server 2012 R2, o anfitrião Hyper-V monta um novo VHD no VM e, em seguida, desmonta-o. Apenas o controlador SCSI pode suportar isto e, portanto, é necessário para cópia de segurança on-line da máquina virtual.  Sem esta definição, o ID 10103 do evento será emitido quando tentar fazer o back up da máquina virtual.)|
|Pré-requisitos do Linux|- Pode apoiar as máquinas virtuais linux utilizando MABS. Apenas são suportados instantâneos consistentes com o ficheiro.|
|Criar cópias de segurança de VMs com o armazenamento CSV|–   Para o armazenamento de CSV, instale o fornecedor de hardware VSS (Serviço de Cópia Sombra de Volumes) no servidor Hyper-V. Contacte o fornecedor da rede de armazenamento (SAN) do fornecedor de hardware VSS.<br />- Se um único nó se desligar inesperadamente num cluster CSV, o MABS irá efetuar uma verificação de consistência contra as máquinas virtuais que estavam a funcionar naquele nó.<br />–   Se precisar de reiniciar um servidor de Hyper-V que tem a Encriptação de Unidade BitLocker ativada no cluster CSV, deve executar uma verificação de consistência nas máquinas virtuais de Hyper-V.|
|Criar cópias de segurança de VMs com armazenamento SMB|–   Ative a montagem automática no servidor que esteja a executar o Hyper-V para ativar a proteção de máquina virtual.<br />   –   Desative a Descarga de Chimney de TCP.<br />–   Certifique-se de que todas as contas de máquina$ de Hyper-V têm permissões completas sobre as partilhas de ficheiros SMB remotos específicas.<br />- Certifique-se de que o caminho de ficheiro para todos os componentes da máquina virtual durante a recuperação para uma localização alternativa é inferior a 260 caracteres. Se não, a recuperação pode ter sucesso, mas o Hyper-V não será capaz de montar a máquina virtual.<br />- Os seguintes cenários não são suportados:<br />     Implantações em que alguns componentes da máquina virtual se encontram em volumes locais e alguns componentes estão em volumes remotos; um endereço IPv4 ou IPv6 para servidor de ficheiros de localização de armazenamento e recuperação de uma máquina virtual para um computador que utiliza ações SMB remotas.<br />- Terá de ativar o serviço de agente VSS do Servidor de Ficheiros em cada servidor SMB - Adicione-o em **funções de Adicionar e funcionalidades** > **Selecione funções** > de ficheiro**seletiva** > **File Services** > **File Service** > seletiva seletiva seletiva seleção de ficheiros de ficheiros de ficheiros**vsS Service**.|

## <a name="back-up-virtual-machines"></a>Criar cópias de segurança de máquinas virtuais

1. Instale o seu [servidor MABS](backup-azure-microsoft-azure-backup.md) e [o seu armazenamento.](backup-mabs-add-storage.md) Quando configurar o seu armazenamento, utilize estas diretrizes para capacidade de armazenamento.
   - Tamanho médio de máquinas virtuais - 100 GB
   - Número de máquinas virtuais por servidor MABS - 800
   - Tamanho total de 800 VMs - 80 TB
   - Espaço necessário para o armazenamento de cópia de segurança - 80 TB

2. Instale o agente de proteção MABS no servidor Hyper-V ou nos nós de cluster Hyper-V. Se estiver a fazer backup ao nível dos hóspedes, vai instalar o agente nos VMs que pretende fazer de reserva ao nível dos hóspedes.

3. Na consola do Administrador MABS, clique em **Proteção** > **Criar grupo de proteção** para abrir o assistente do Grupo de **Proteção Nova.**

4. Na página **Selecionar Membros do Grupo**, selecione as VMs que pretende proteger a partir dos servidores de anfitrião de Hyper-V em que se encontram. Recomendamos que coloque todas as VMs com a mesma política de proteção num grupo de proteção. Para uma utilização eficaz do espaço, ative a colocalização. A colocalização permite-lhe localizar dados a partir de diferentes grupos de proteção no mesmo armazenamento em disco ou em banda, de modo a que múltiplas origens de dados tenham uma réplica e um volume de ponto de recuperação únicos.

5. Na página **Selecionar Método de Proteção de Dados** , especifique o nome do grupo de proteção. Selecione **Pretendo proteção de curta duração com disco** e **Pretendo proteção online** se pretender fazer uma cópia de segurança dos dados para o Azure ao utilizar o serviço do Microsoft Azure Backup.

6. Em especificar a gama de > **retenção** **de objetivos de curto prazo,** especifique quanto tempo pretende reter os dados do disco. Na **frequência de sincronização,** especifique quantas cópias incrementais dos dados devem ser executadas. Em alternativa, em vez de selecionar um intervalo para as cópias de segurança incrementais, pode ativar **Apenas antes de um ponto de recuperação**. Com esta definição ativada, o MABS executará um backup completo expresso imediatamente antes de cada ponto de recuperação programado.

    > [!NOTE]
    >
    >Se estiver a proteger cargas de trabalho da aplicação, os pontos de recuperação são criados com frequência de Sincronização, desde que a aplicação suporte cópias de segurança incrementais. Se não o fizer, então a MABS executa uma cópia de segurança expressa, em vez de uma cópia de segurança incremental, e cria pontos de recuperação de acordo com o calendário de backup expresso.

7. Na página de atribuição de **discos de revisão,** reveja o espaço de disco de armazenamento atribuído ao grupo de proteção.

   **O tamanho total de Dados** é o tamanho dos dados que pretende fazer cópia sinuoso, e o espaço do Disco a fornecer no **MABS** é o espaço que o MABS recomenda para o grupo de proteção. O MABS escolhe o volume de backup ideal, com base nas definições. No entanto, pode editar as opções de cópia de segurança de volume em **Detalhes de atribuição do disco**. Para cargas de trabalho, selecione o armazenamento preferido no menu de lista pendente. As edições alteram os valores para **Armazenamento Total** e **Armazenamento Gratuito** no painel **Armazenamento em Disco Disponível**. Espaço subprovisionado é a quantidade de armazenamento MABS sugere que adicione ao volume, para continuar com backups suavemente no futuro.

8. Na página **Escolher Método de Criação de Réplica** , especifique como será efetuada a replicação inicial de dados no grupo de proteção. Se selecionar para **replicar automaticamente sobre a rede,** recomendamos que escolha um tempo fora do pico. Para grandes quantidades de dados ou menos do que as condições ideais de rede, considere selecionar **Manualmente**, o que requer replicar os dados offline utilizando meios amovíveis.

9. Na página **Opções de Verificação de Consistência** , selecione como pretende automatizar as verificações de consistência. Pode ativar a execução de uma verificação apenas quando os dados de réplica ficarem inconsistentes ou de acordo com uma agenda. Se não quiser configurar verificações de consistência automáticas, pode executar uma verificação manual em qualquer altura, clicando com o botão direito do rato no grupo de proteção e selecionando **Efetuar Verificação de Consistência**.

    Após criar o grupo de proteção, ocorre a replicação inicial dos dados, de acordo com o método que selecionou. Após a replicação inicial, as cópias de segurança são criadas de acordo com as definições do grupo de proteção. Se necessitar de recuperar os dados com backup, note o seguinte:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Máquinas virtuais em cópia de segurança configuradas para a migração em direto

Quando as máquinas virtuais estão envolvidas na migração ao vivo, o MABS continua a proteger as máquinas virtuais enquanto o agente de proteção MABS for instalado no hospedeiro Hyper-V. A forma como o MABS protege as máquinas virtuais depende do tipo de migração viva envolvida.

**Migração viva dentro** de um cluster - Quando uma máquina virtual é migrada dentro de um cluster MABS deteta a migração, e apoia a máquina virtual a partir do novo nó de cluster sem qualquer requisito de intervenção do utilizador. Como o local de armazenamento não mudou, o MABS continua com cópias de segurança expressas.

**Migração ao vivo fora do cluster** - Quando uma máquina virtual é migrada entre servidores autónomos, diferentes clusters, ou entre um servidor autónomo e um cluster, o MABS deteta a migração e pode apoiar a máquina virtual sem a intervenção do utilizador.

### <a name="requirements-for-maintaining-protection"></a>Requisitos para manter a proteção

Seguem-se os requisitos para manter a proteção durante a migração em direto:

- Os anfitriões Hyper-V para as máquinas virtuais devem estar localizados numa nuvem VMM do System Center, num servidor VMM, funcionando pelo menos o System Center 2012 com SP1.

- O agente de proteção MABS deve ser instalado em todos os anfitriões hyper-V.

- Os servidores MABS devem ser ligados ao servidor VMM. Todos os servidores de anfitriões Hyper-V na nuvem VMM também devem estar ligados aos servidores MABS. Isto permite que o MABS se comunique com o servidor VMM para que o MABS possa descobrir em que servidor de anfitriões Hyper-V a máquina virtual está atualmente a funcionar, e criar uma nova cópia de segurança desse servidor Hyper-V. Se uma ligação não puder ser estabelecida no servidor Hyper-V, a cópia de segurança falha com uma mensagem de que o agente de proteção MABS está incontactável.

- Todos os servidores MABS, servidores VMM e servidores de anfitriões Hyper-V devem estar no mesmo domínio.

### <a name="details-about-live-migration"></a>Detalhes sobre a migração em direto

Para cópias de segurança durante a migração em direto, note o seguinte:

- Se uma migração ao vivo transfere o armazenamento, o MABS realiza uma verificação de consistência total da máquina virtual, e depois continua com cópias de segurança expressas. Quando ocorre uma migração ao vivo do armazenamento, o Hyper-V reorganiza o disco rígido virtual (VHD) ou VHDX, o que provoca um aumento único no tamanho dos dados de backup DoMABS.

- No anfitrião de máquina virtual, ative a montagem automática para ativar a proteção virtual e desativar a Descarga de Chimney de TCP.

- O MABS utiliza a porta 6070 como porta padrão para hospedar o Serviço de Ajuda DPM-VMM. Para alterar o registo:

    1. Navegue para **HKLM\Software\Microsoft\Microsoft Data Protection Manager\Configuração**.
    2. Crie um valor DWORD de 32 bits: DpmVmmHelperServicePort e escreva o número da porta atualizado como parte da chave do registo.
    3. Abra ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config``` e altere o número da porta, de 6070 para a porta nova. Por exemplo: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Reinicie o Serviço Auxiliar do DPM-VMM e reinicie o serviço DPM.

### <a name="set-up-protection-for-live-migration"></a>Configurar proteção para migração em direto

Para configurar a proteção para migração em direto:

1. Instale o servidor MABS e o seu armazenamento e instale o agente de proteção MABS em todos os servidores ou nódeo cluster Hyper-V na nuvem VMM. Se estiver a utilizar o armazenamento SMB num cluster, instale o agente de proteção MABS em todos os nós do cluster.

2. Instale a consola VMM como componente cliente no servidor MABS para que o MABS possa comunicar com o servidor VMM. A consola deve ser da mesma versão que está em execução no servidor VMM.

3. Atribuir a conta MABSMachineName$ como uma conta de administrador apenas de leitura no servidor de gestão VMM.

4. Ligue todos os servidores de anfitriões Hyper-V a todos os servidores MABS com o `Set-DPMGlobalProperty` cmdlet PowerShell. O cmdlet aceita vários nomes de servidorEs MABS. Utilize o formato: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Para mais informações, consulte [Set-DPMGlobalProperty](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmglobalproperty?view=systemcenter-ps-2019).

5. Depois de todas as máquinas virtuais em execução nos anfitriões Hyper-V nas nuvens VMM serem detetadas no VMM, configure um grupo de proteção e adicione as máquinas virtuais que pretende proteger. Devem ser ativados controlos automáticos de coerência ao nível do grupo de proteção para proteção em cenários de mobilidade virtual das máquinas.

6. Depois de configuradas as definições, quando uma máquina virtual migra de um cluster para outro todas as cópias de segurança continuam como esperado. Pode verificar se a migração em direto se encontra ativada conforme esperado da seguinte forma:

   1. Certifique-se de que o Serviço Auxiliar do DPM-VMM se encontra em execução. Se não for, começa.

   2. Abra o Microsoft SQL Server Management Studio e ligue-se à instância que acolhe a base de dados MABS (DPMDB). No DPMDB, execute a seguinte consulta: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Esta consulta contém uma `KnownVMMServer`propriedade chamada . Este valor deverá ser o mesmo valor que forneceu com o cmdlet `Set-DPMGlobalProperty`.

   3. Execute a seguinte consulta para validar o parâmetro *VMMIdentifier* no `PhysicalPathXML` para uma máquina virtual específica. Substitua `VMName` pelo nome da máquina virtual.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Abra o ficheiro .xml devolvido por esta consulta e valide se o campo *VMMIdentifier* tem um valor.

### <a name="run-manual-migration"></a>Executar migração manual

Depois de completar os passos nas secções anteriores, e o trabalho do MABS Summary Manager terminar, a migração está ativada. Por predefinição, esta tarefa é iniciada à meia-noite e é executada todas as manhãs. Se quiser executar uma migração manual para certificar-se de que tudo está a funcionar conforme esperado, faça o seguinte:

1. Abra o Estúdio de Gestão de Servidores SQL e ligue-se à instância que acolhe a base de dados MABS.

2. Execute a seguinte consulta: `SELECT SCH.ScheduleId FROM tbl_JM_JobDefinition JD JOIN tbl_SCH_ScheduleDefinition SCH ON JD.JobDefinitionId = SCH.JobDefinitionId WHERE JD.Type = '282faac6-e3cb-4015-8c6d-4276fcca11d4' AND JD.IsDeleted = 0 AND SCH.IsDeleted = 0`. Esta consulta devolve o **ScheduleID**. Anote este ID, uma vez que irá utilizá-lo no passo seguinte.

3. No SQL Server Management Studio, expanda **SQL Server Agent** e expanda **Tarefas**. Clique com o botão direito do rato no **ScheduleID** que anotou e selecione **Iniciar Tarefa no Passo**.

O desempenho de reserva é afetado quando o trabalho funciona. O tamanho e a escala da implementação determinam quanto tempo a tarefa demora a ser concluída.

## <a name="back-up-replica-virtual-machines"></a>Criar cópias de segurança de máquinas virtuais de réplica

Se o MABS estiver a funcionar no Windows Server 2012 R2 ou superior, então pode fazer o back-up de máquinas virtuais de réplica. Isto é útil por diversas razões:

**Reduz o impacto das cópias de segurança na carga de trabalho em execução** – a criação de uma cópia de segurança de uma máquina virtual incorre em overhead à medida que se cria um instantâneo. Ao descarregar o processo de backup para um local remoto secundário, a carga de trabalho de funcionamento já não é afetada pela operação de backup. Isto é apenas aplicável a implementações nas quais a cópia de segurança é armazenada num site remoto. Por exemplo, pode criar cópias de segurança diariamente e armazenar dados localmente para garantir restauros rápidos, mas criar cópias de segurança de máquinas virtuais de réplica armazenadas para retenção de longa duração mensais ou trimestrais.

**Poupança de largura de banda** – numa implementação remota típica de sucursal/sede, precisa de uma quantidade apropriada de largura de banda aprovisionada para transferir os dados das cópias de segurança entre sites. Se criar uma estratégia de replicação e ativação pós-falha, além da sua estratégia de cópia de segurança dos dados, pode reduzir a quantidade de dados redundantes enviados através da rede. Ao fazer backup dos dados da réplica virtual da máquina em vez do primário, guarda-se a sobrecarga de envio dos dados de backup através da rede.

**Ativa a cópia de segurança do fornecedor de serviços de alojamento** -pode utilizar um centro de dados alojado como um site de réplica, sem ser necessário um centro de dados secundário. Neste caso, o anfitrião SLA requer uma cópia de segurança consistente das máquinas virtuais de réplica.

A máquina virtual de réplica está desativada até à ocorrência de uma ativação pós-falha, e o VSS não consegue garantir cópias de segurança consistentes de uma máquina virtual de réplica. Deste modo, as cópias de segurança de uma máquina virtual de réplica apenas serão consistentes com falhas. Se não for possível garantir a consistência com falhas, a cópia de segurança falha e esta situação pode ocorrer em várias condições:

- A máquina virtual de réplica não está em bom estado de funcionamento, mas sim em estado crítico.

- A máquina virtual de réplica está a ressincronizar (no estado Ressincronização em Curso ou Ressincronização Necessária).

- A replicação inicial entre o site primário e secundário está em curso ou pendente para a máquina virtual.

- Os registos .hrl estão a ser aplicados à máquina virtual da réplica, ou uma ação anterior para aplicar os troncos .hrl no disco virtual falhou, ou foi cancelado ou interrompido.

- A migração ou a ativação pós-falha da máquina virtual de réplica estão em curso

## <a name="recover-backed-up-virtual-machines"></a>Recuperar máquinas virtuais em cópia de segurança

Quando for possível recuperar uma máquina virtual com cópia de segurança, utilize o assistente de Recuperação para selecionar a máquina virtual e o ponto de recuperação específico. Para abrir o Assistente de Recuperação e recuperar uma máquina virtual:

1. Na consola do Administrador MABS, escreva o nome do VM ou expanda a lista de itens protegidos e selecione o VM que pretende recuperar.

2. No painel **Pontos de recuperação para**, no calendário, clique em qualquer data para ver os pontos de recuperação disponíveis. Em seguida, no painel **Caminho**, selecione o ponto de recuperação que pretende utilizar no assistente de Recuperação.

3. No menu **Ações**, clique em **Recuperar** para abrir o Assistente de Recuperação.

    A VM e o ponto de recuperação que selecionou são apresentados no ecrã **Rever Seleção de Recuperação**. Clique em **Seguinte**.

4. No ecrã **Selecionar Tipo de Recuperação**, selecione onde pretende restaurar os dados e clique em **Seguinte**.

    - **Recuperar para a instância original**: quando recuperar para a instância original, o VHD será eliminado. O MABS recupera o VHD e outros ficheiros de configuração para a localização original utilizando o escritor Hyper-V VSS. No final do processo de recuperação, as máquinas virtuais continuam a ter elevada disponibilidade.
        O grupo de recursos tem de estar presente para a recuperação. Se não estiver disponível, recupere para uma localização alternativa e torne a máquina virtual altamente disponível.

    - **Recupere como máquina virtual para qualquer hospedeiro:** O MABS suporta a recuperação alternativa de localização (ALR), que proporciona uma recuperação perfeita de uma máquina virtual Hiper-V protegida para um diferente hospedeiro Hyper-V, independente da arquitetura do processador. As máquinas virtuais do Hyper-V que forem recuperadas num nó de cluster não terão elevada disponibilidade. Se escolher esta opção, o Assistente de Recuperação apresenta um ecrã adicional para identificar o destino e o caminho de destino.

    - **Cópia para uma pasta**de rede : O MABS suporta a recuperação ao nível do item (ILR), que permite fazer a recuperação ao nível do item de ficheiros, pastas, volumes e discos rígidos virtuais (VHDs) de uma cópia de segurança de nível de suporte de máquinas virtuais Hyper-V para uma partilha de rede ou um volume num servidor protegido mABS. O agente de proteção MABS não tem de ser instalado no interior do hóspede para realizar a recuperação do nível do item. Se escolher esta opção, o Assistente de Recuperação apresenta um ecrã adicional para identificar o destino e o caminho de destino.

5. Em **Especificar Opções de Recuperação**, configure as opções de recuperação e clique em **Seguinte**:

    - Se estiver a recuperar uma VM ao longo da largura de banda reduzida, clique em **Modificar** para ativar a **Limitação da utilização de largura de banda**. Depois de ativar a opção de limitação, pode especificar a quantidade de largura de banda que pretende disponibilizar e a hora a que essa largura de banda fica disponível.
    - Selecione **Ativar a recuperação baseada em SAN utilizando instantâneos de hardware**, se tiver configurado a sua rede.
    - Selecione **Enviar um e-mail ao concluir a recuperação** e forneça os endereços de e-mail, se pretender que sejam enviadas notificações de -mail ao concluir o processo de recuperação.

6. No ecrã Resumo, certifique-se de que todos os detalhes estão corretos. Se os detalhes estiverem incorretos ou se pretender efetuar uma alteração, clique em **Anterior**. Se estiver satisfeito com as definições, clique em **Recuperar** para iniciar o processo de recuperação.

7. O ecrã **Estado de Recuperação** fornece informações sobre a tarefa de recuperação.

## <a name="next-steps"></a>Passos seguintes

[Recuperar dados do Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
