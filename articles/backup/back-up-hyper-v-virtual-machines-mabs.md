---
title: Apoiar máquinas virtuais Hiper-V com MABS
description: Este artigo contém os procedimentos de backup e recuperação de máquinas virtuais utilizando o Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: a020559229771fff1ecc8fb512a5b2af70240cdd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102509511"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Faça backup de máquinas virtuais Hyper-V com servidor de backup Azure

Este artigo explica como fazer backup de máquinas virtuais Hiper-V utilizando o Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Cenários suportados

O MABS pode fazer o back up de máquinas virtuais em funcionamento em servidores anfitriões Hiper-V nos seguintes cenários:

- **Máquinas virtuais com armazenamento local ou direto** – crie uma cópia de segurança das máquinas virtuais alojadas em servidores autónomos do Hyper-V que possuam armazenamento local ou ligado diretamente. Por exemplo: um disco rígido, um dispositivo de rede de área de armazenamento (SAN) ou um dispositivo de armazenamento (NAS) ligado à rede. O agente de proteção MABS deve ser instalado em todos os anfitriões.

- **Máquinas virtuais num cluster com armazenamento CSV** – crie uma cópia de segurança das máquinas virtuais alojadas num cluster do Hyper-V com o armazenamento Volume Partilhado de Cluster (CSV). O agente de proteção MABS está instalado em cada nó de cluster.

## <a name="host-versus-guest-backup"></a>Anfitrião versus apoio de hóspedes

O MABS pode fazer uma cópia de segurança de nível de anfitrião ou de nível de hóspedes de VMs Hiper-V. Ao nível do anfitrião, o agente de proteção MABS é instalado no servidor ou cluster do anfitrião Hiper-V e protege todos os VMs e ficheiros de dados em execução nesse hospedeiro.   Ao nível do hóspede, o agente é instalado em cada máquina virtual e protege a carga de trabalho presente nessa máquina.

Ambos os métodos têm vantagens e desvantagens:

- As cópias de segurança ao nível do hospedeiro são flexíveis porque funcionam independentemente do tipo de SISTEMA que funciona nas máquinas convidadas e não requerem a instalação do agente de proteção MABS em cada VM. Se implementar a cópia de segurança do nível do anfitrião, pode recuperar uma máquina virtual inteira, ou ficheiros e pastas (recuperação ao nível do item).

- A cópia de segurança ao nível do hóspede é útil se pretender proteger cargas de trabalho específicas em funcionamento numa máquina virtual. Ao nível do anfitrião pode recuperar um VM inteiro ou ficheiros específicos, mas não proporcionará recuperação no contexto de uma aplicação específica. Por exemplo, para recuperar itens específicos do SharePoint de um VM apoiado, deve fazer a cópia de segurança ao nível do hóspede desse VM. Utilize a cópia de segurança ao nível do hóspede se quiser proteger os dados armazenados em discos passthrough. O Passthrough permite que a máquina virtual aceda diretamente ao dispositivo de armazenamento e não armazena dados de volume virtual num ficheiro VHD.

## <a name="how-the-backup-process-works"></a>Como funciona o processo de backup

MABS executa backup com VSS da seguinte forma. Os passos nesta descrição estão numerados para o ajudar com clareza.

1. O motor de sincronização baseado em bloco MABS faz uma cópia inicial da máquina virtual protegida e garante que a cópia da máquina virtual é completa e consistente.

2. Após a cópia inicial ser feita e verificada, o MABS utiliza o escritor Hiper-V VSS para capturar cópias de segurança. O escritor VSS fornece um conjunto consistente de dados de blocos de disco sincronizados com o servidor MABS. Esta abordagem proporciona o benefício de uma "cópia de segurança completa" com o servidor MABS, ao mesmo tempo que minimiza os dados de backup que devem ser transferidos através da rede.

3. O agente de proteção MABS num servidor que está a executar o Hyper-V utiliza as APIs hiper-V existentes para determinar se uma máquina virtual protegida também suporta VSS.

   - Se uma máquina virtual cumprir os requisitos para uma cópia de segurança online e tiver o componente dos serviços de integração do Hyper-V instalado, o escritor VSS do Hyper-V reencaminha recursivamente o pedido VSS para todos os processos com deteção de VSS na máquina virtual. Esta operação ocorre sem que o agente de proteção MABS seja instalado na máquina virtual. Este pedido do VSS recursivo permite que o escritor VSS do Hyper-V confirme que as operações de escrita no disco estão sincronizadas de forma a capturar o instantâneo do VSS sem ocorrer a perda de dados.

     O componente dos serviços de integração do Hyper-V invoca o escritor de VSS do Hyper-V nos Serviços de Cópia Sombra de Volumes (VSS) nas máquinas virtuais para assegurar que os respetivos dados de aplicação estão num estado consistente.

   - Se a máquina virtual não cumprir os requisitos de backup on-line, o MABS utiliza automaticamente as APIs Hiper-V para interromper a máquina virtual antes de capturar ficheiros de dados.

4. Após a cópia inicial da base da máquina virtual sincronizar com o servidor MABS, todas as alterações que são feitas aos recursos da máquina virtual são capturadas num novo ponto de recuperação. O ponto de recuperação representa o estado consistente da máquina virtual num momento específico. As capturas do ponto de recuperação podem ocorrer, pelo menos, uma vez por dia. Quando um novo ponto de recuperação é criado, o MABS usa a replicação de nível de bloco em conjunto com o escritor Hiper-V VSS para determinar quais os blocos que foram alterados no servidor que está a executar Hyper-V após a criação do último ponto de recuperação. Estes blocos de dados são então transferidos para o servidor MABS e são aplicados à réplica dos dados protegidos.

5. O servidor MABS utiliza VSS nos volumes que hospedam dados de recuperação para que estejam disponíveis várias cópias-sombra. Cada uma destas cópias sombra fornece uma recuperação separada. Os pontos de recuperação vss são armazenados no servidor MABS. A cópia temporária feita no servidor em funcionamento do Hyper-V só é armazenada durante a sincronização do MABS.

>[!NOTE]
>
>A partir do Windows Server 2016, os discos rígidos virtuais Hiper-V têm um rastreio de alterações incorporado conhecido como rastreio de alterações resilientes (RCT). O MABS utiliza o RCT (o rastreio de alterações nativas em Hiper-V), o que diminui a necessidade de verificações de consistência demoradas em cenários como acidentes com VM. O RCT proporciona uma melhor resiliência do que o controlo de alterações fornecido pelas cópias de segurança baseadas em instantâneos do VSS. O MABS V3 otimiza ainda mais o consumo de rede e armazenamento, transferindo apenas os dados alterados durante quaisquer verificações de consistência.

## <a name="backup-prerequisites"></a>Pré-requisitos da cópia de segurança

Estes são os pré-requisitos para o backup de máquinas virtuais Hiper-V com MABS:

|Pré-requisito|Detalhes|
|------------|-------|
|Pré-requisitos do MABS|- Se pretender efetuar uma recuperação ao nível do item para máquinas virtuais (recuperar ficheiros, pastas, volumes), terá de instalar a função Hyper-V no servidor MABS.  Se só quer recuperar a máquina virtual e não o nível de item, então a função não é necessária.<br />- Pode proteger até 800 máquinas virtuais de 100 GB cada um num servidor MABS e permitir vários servidores MABS que suportam clusters maiores.<br />- O MABS exclui o ficheiro de página de cópias de segurança incrementais para melhorar o desempenho de backup da máquina virtual.<br />- O MABS pode fazer uma cobertura de um servidor Hiper-V ou um cluster no mesmo domínio que o servidor MABS, ou num domínio infantil ou fidedigno. Se quiser fazer cópias de hiper-V num grupo de trabalho ou num domínio não fidedqui o caso, terá de configurar a autenticação. Para um único servidor Hyper-V, pode utilizar a autenticação NTLM ou certificado. Para um cluster, só pode utilizar a autenticação de certificados.<br />–   Não é suportada a utilização de cópias de segurança ao nível do anfitrião para criar cópias de segurança de máquinas virtuais em discos pass-through. Neste cenário, recomendamos que utilize backup de nível de anfitrião para fazer backup de ficheiros VHD e backup de nível de hóspedes para fazer o backup de outros dados que não são visíveis no anfitrião.<br />   -Pode fazer o reforço dos VM armazenados em volumes deduplicados.|
|Pré-requisitos de VM do Hyper-V|- A versão dos Componentes de Integração que está a funcionar na máquina virtual deve ser a mesma que a versão do anfitrião Hyper-V. <br />–   Irá precisar de espaço livre no volume que está a alojar os ficheiros virtuais do disco rígido em cada cópia de segurança das máquinas virtuais para que o Hyper-V tenha espaço suficiente para discos de diferenciação (AVHD) durante a cópia de segurança. O espaço deve ser pelo menos igual ao cálculo Do tamanho inicial do disco Tempo de cópia da janela **\* de \* backup.** Se estiver a executar múltiplas cópias de segurança num cluster, irá precisar de capacidade de armazenamento suficiente para acomodar os ficheiros AVHD nas máquinas virtuais que utilizem este cálculo.<br />- Para fazer o back up de máquinas virtuais localizadas em servidores anfitriões Hiper-V que executam o Windows Server 2012 R2, a máquina virtual deve ter um controlador SCSI especificado, mesmo que não esteja ligado a nada. (No Windows Server 2012 R2 de backup on-line, o anfitrião Hyper-V monta um novo VHD no VM e, em seguida, desmonta-o. Apenas o controlador SCSI pode apoiá-lo e, portanto, é necessário para a cópia de segurança online da máquina virtual.  Sem esta definição, o ID 10103 do evento será emitido quando tentar fazer o back up da máquina virtual.)|
|Pré-requisitos do Linux|- Pode apoiar as máquinas virtuais Do Linux utilizando o MABS. Apenas são suportados instantâneos consistentes com o ficheiro.|
|Criar cópias de segurança de VMs com o armazenamento CSV|–   Para o armazenamento de CSV, instale o fornecedor de hardware VSS (Serviço de Cópia Sombra de Volumes) no servidor Hyper-V. Contacte o fornecedor da rede de armazenamento (SAN) do fornecedor de hardware VSS.<br />- Se um único nó se desligar inesperadamente num cluster CSV, o MABS efetuará uma verificação de consistência contra as máquinas virtuais que estavam a funcionar nesse nó.<br />–   Se precisar de reiniciar um servidor de Hyper-V que tem a Encriptação de Unidade BitLocker ativada no cluster CSV, deve executar uma verificação de consistência nas máquinas virtuais de Hyper-V.|
|Criar cópias de segurança de VMs com armazenamento SMB|- Ligue automaticamente o servidor que está a executar o Hyper-V para permitir a proteção da máquina virtual.<br />   –   Desative a Descarga de Chimney de TCP.<br />–   Certifique-se de que todas as contas de máquina$ de Hyper-V têm permissões completas sobre as partilhas de ficheiros SMB remotos específicas.<br />- Certifique-se de que o caminho do ficheiro para todos os componentes da máquina virtual durante a recuperação para uma localização alternativa é inferior a 260 caracteres. Se não, a recuperação pode ter sucesso, mas o Hyper-V não será capaz de montar a máquina virtual.<br />- Os seguintes cenários não são apoiados:<br />     Implementações em que alguns componentes da máquina virtual se encontram em volumes locais e alguns componentes estão em volumes remotos; um endereço IPv4 ou IPv6 para servidor de ficheiros de localização de armazenamento e recuperação de uma máquina virtual para um computador que utiliza partilhas remotas de SMB.<br />- Terá de ativar o serviço de agente VSS do Servidor de Ficheiros VSS em cada servidor SMB - Adicione-o em **funções de adicionar e**  >  **funcionalidades Selecione funções de servidor** Ficheiro e Serviços de Armazenamento Serviço de  >    >    >    >  **Ficheiros Serviços VSS Serviço vss**.|

## <a name="back-up-virtual-machines"></a>Criar cópias de segurança de máquinas virtuais

1. Configurar o seu [servidor MABS](backup-azure-microsoft-azure-backup.md) e [o seu armazenamento.](backup-mabs-add-storage.md) Quando configurar o seu armazenamento, utilize estas diretrizes para capacidade de armazenamento.
   - Tamanho médio de máquinas virtuais - 100 GB
   - Número de máquinas virtuais por servidor MABS - 800
   - Tamanho total de 800 VMs - 80 TB
   - Espaço necessário para o armazenamento de cópia de segurança - 80 TB

2. Instale o agente de proteção MABS nos nós do servidor Hiper-V ou hiper-V. Se estiver a fazer backup ao nível de hóspedes, instalará o agente nos VMs que pretende apoiar ao nível dos hóspedes.

3. Na consola MABS Administrator, selecione **Protection**  >  **Create protection group** para abrir o assistente criar novo grupo de **proteção.**

4. Na página **Selecionar Membros do Grupo**, selecione as VMs que pretende proteger a partir dos servidores de anfitrião de Hyper-V em que se encontram. Recomendamos que coloque todas as VMs com a mesma política de proteção num grupo de proteção. Para uma utilização eficaz do espaço, ative a colocalização. A colocalização permite-lhe localizar dados a partir de diferentes grupos de proteção no mesmo armazenamento em disco ou em banda, de modo a que múltiplas origens de dados tenham uma réplica e um volume de ponto de recuperação únicos.

5. Na página **Selecionar Método de Proteção de Dados** , especifique o nome do grupo de proteção. Selecione **Pretendo proteção de curta duração com disco** e **Pretendo proteção online** se pretender fazer uma cópia de segurança dos dados para o Azure ao utilizar o serviço do Microsoft Azure Backup.

6. No **intervalo De retenção de objetivos Short-Term,**  >  especifique quanto tempo pretende reter dados de disco. Na **frequência de sincronização,** especifique com que frequência as cópias de segurança incrementais dos dados devem ser executadas. Em alternativa, em vez de selecionar um intervalo para as cópias de segurança incrementais, pode ativar **Apenas antes de um ponto de recuperação**. Com esta definição ativada, o MABS executará uma cópia de segurança completa expressa pouco antes de cada ponto de recuperação programado.

    > [!NOTE]
    >
    >Se estiver a proteger cargas de trabalho da aplicação, os pontos de recuperação são criados com frequência de Sincronização, desde que a aplicação suporte cópias de segurança incrementais. Se não o fizer, então o MABS executa uma cópia de segurança expressa completa, em vez de uma cópia de segurança incremental, e cria pontos de recuperação de acordo com o calendário de backup expresso.

7. Na página de atribuição do **disco Review,** reveja o espaço do disco de armazenamento atribuído ao grupo de proteção.

   **O tamanho total** dos Dados é o tamanho dos dados que pretende fazer e o **espaço do disco a ser a provisionado no MABS** é o espaço que o MABS recomenda para o grupo de proteção. O MABS escolhe o volume de backup ideal, com base nas definições. No entanto, pode editar as opções de cópia de segurança de volume em **Detalhes de atribuição do disco**. Para cargas de trabalho, selecione o armazenamento preferido no menu de lista pendente. As edições alteram os valores para **Armazenamento Total** e **Armazenamento Gratuito** no painel **Armazenamento em Disco Disponível**. Espaço subprovisionado é a quantidade de armazenamento que o MABS sugere que adicione ao volume, para continuar com cópias de segurança suavemente no futuro.

8. Na página **Escolher Método de Criação de Réplica** , especifique como será efetuada a replicação inicial de dados no grupo de proteção. Se selecionar para **replicar automaticamente sobre a rede,** recomendamos que escolha um tempo fora do pico. Para grandes quantidades de dados ou menos do que as condições ideais de rede, considere selecionar **manualmente**, o que requer replicar os dados offline usando suportes amovíveis.

9. Na página **Opções de Verificação de Consistência** , selecione como pretende automatizar as verificações de consistência. Pode ativar a execução de uma verificação apenas quando os dados de réplica ficarem inconsistentes ou de acordo com uma agenda. Se não quiser configurar verificações de consistência automáticas, pode executar uma verificação manual em qualquer altura, clicando com o botão direito do rato no grupo de proteção e selecionando **Efetuar Verificação de Consistência**.

    Após criar o grupo de proteção, ocorre a replicação inicial dos dados, de acordo com o método que selecionou. Após a replicação inicial, as cópias de segurança são criadas de acordo com as definições do grupo de proteção. Se necessitar de recuperar dados com o armazenamento, tome nota do seguinte:

## <a name="back-up-replica-virtual-machines"></a>Criar cópias de segurança de máquinas virtuais de réplica

Se o MABS estiver a funcionar no Windows Server 2012 R2 ou superior, então pode fazer uma parte de trás de máquinas virtuais réplicas. Isto é útil por diversas razões:

**Reduz o impacto das cópias de segurança na carga de trabalho em execução** – a criação de uma cópia de segurança de uma máquina virtual incorre em overhead à medida que se cria um instantâneo. Ao descarregar o processo de backup para um local remoto secundário, a carga de trabalho em execução já não é afetada pela operação de backup. Isto é apenas aplicável a implementações nas quais a cópia de segurança é armazenada num site remoto. Por exemplo, pode criar cópias de segurança diariamente e armazenar dados localmente para garantir restauros rápidos, mas criar cópias de segurança de máquinas virtuais de réplica armazenadas para retenção de longa duração mensais ou trimestrais.

**Poupança de largura de banda** – numa implementação remota típica de sucursal/sede, precisa de uma quantidade apropriada de largura de banda aprovisionada para transferir os dados das cópias de segurança entre sites. Se criar uma estratégia de replicação e ativação pós-falha, além da sua estratégia de cópia de segurança dos dados, pode reduzir a quantidade de dados redundantes enviados através da rede. Ao fazer o backup dos dados da réplica da máquina virtual em vez dos primários, evita-se o envio dos dados de backup através da rede.

**Ativa a cópia de segurança do fornecedor de serviços de alojamento** -pode utilizar um centro de dados alojado como um site de réplica, sem ser necessário um centro de dados secundário. Neste caso, o hoster SLA requer uma cópia de segurança consistente de máquinas virtuais réplicas.

A máquina virtual de réplica está desativada até à ocorrência de uma ativação pós-falha, e o VSS não consegue garantir cópias de segurança consistentes de uma máquina virtual de réplica. Assim, a cópia de segurança de uma máquina virtual réplica será apenas consistente com acidentes. Se não for possível garantir a consistência com falhas, a cópia de segurança falha e esta situação pode ocorrer em várias condições:

- A máquina virtual de réplica não está em bom estado de funcionamento, mas sim em estado crítico.

- A máquina virtual de réplica está a ressincronizar (no estado Ressincronização em Curso ou Ressincronização Necessária).

- A replicação inicial entre o site primário e secundário está em curso ou pendente para a máquina virtual.

- .hrl logs estão sendo aplicados na máquina virtual réplica, ou uma ação anterior para aplicar os registos .hrl no disco virtual falhou, ou foi cancelado ou interrompido.

- A migração ou a ativação pós-falha da máquina virtual de réplica estão em curso

## <a name="recover-backed-up-virtual-machines"></a>Recuperar máquinas virtuais em cópia de segurança

Quando for possível recuperar uma máquina virtual com cópia de segurança, utilize o assistente de Recuperação para selecionar a máquina virtual e o ponto de recuperação específico. Para abrir o Assistente de Recuperação e recuperar uma máquina virtual:

1. Na consola MABS Administrator, escreva o nome do VM ou expanda a lista de itens protegidos e selecione o VM que pretende recuperar.

2. Nos **pontos de recuperação do** painel, no calendário, selecione qualquer data para ver os pontos de recuperação disponíveis. Em seguida, no painel **Caminho**, selecione o ponto de recuperação que pretende utilizar no assistente de Recuperação.

3. No menu **Ações,** selecione **Recuperar** para abrir o Assistente de Recuperação.

    A VM e o ponto de recuperação que selecionou são apresentados no ecrã **Rever Seleção de Recuperação**. Selecione **Seguinte**.

4. No ecrã **Select Recovery Type,** selecione onde pretende restaurar os dados e, em seguida, selecione **Seguinte**.

    - **Recupere para o caso original**: Quando se recupera para a instância original, o VHD original e todos os pontos de verificação associados são eliminados. O MABS recupera o VHD e outros ficheiros de configuração para a localização original utilizando o escritor Hyper-V VSS. No final do processo de recuperação, as máquinas virtuais continuam a ter elevada disponibilidade.
        O grupo de recursos tem de estar presente para a recuperação. Se não estiver disponível, recupere para uma localização alternativa e torne a máquina virtual altamente disponível.

    - **Recupere como máquina virtual para qualquer anfitrião**: O MABS suporta a recuperação alternativa da localização (ALR), que proporciona uma recuperação perfeita de uma máquina virtual Hiper-V protegida a um anfitrião Hiper-V diferente, independente da arquitetura do processador. As máquinas virtuais hiper-V que são recuperadas num nó de cluster não estarão altamente disponíveis. Se escolher esta opção, o Assistente de Recuperação apresenta um ecrã adicional para identificar o destino e o caminho de destino.
    
        >[!NOTE]
        >Se selecionar o anfitrião original, o comportamento é o mesmo que **recuperar para a instância original.** O VHD original e todos os pontos de verificação associados serão eliminados.

    - **Cópia para uma pasta** de rede : O MABS suporta a recuperação do nível de item (ILR), que permite fazer a recuperação ao nível do item de ficheiros, pastas, volumes e discos rígidos virtuais (VHDs) de uma cópia de segurança ao nível do hospedeiro de máquinas virtuais Hyper-V para uma partilha de rede ou um volume num servidor protegido pelo MABS. O agente de proteção MABS não precisa de ser instalado dentro do hóspede para realizar a recuperação ao nível do item. Se escolher esta opção, o Assistente de Recuperação apresenta um ecrã adicional para identificar o destino e o caminho de destino.

5. Em **Especificar Opções de Recuperação** configurar as opções de recuperação e selecionar **Seguinte**:

    - Se estiver a recuperar um VM em relação à largura de banda baixa, selecione **Modificar** para ativar o **estrangulamento de utilização da largura de banda da rede**. Depois de ativar a opção de limitação, pode especificar a quantidade de largura de banda que pretende disponibilizar e a hora a que essa largura de banda fica disponível.
    - Selecione **Ativar a recuperação baseada em SAN utilizando imagens** de hardware se tiver configurado a sua rede.
    - Selecione **Enviar um e-mail ao concluir a recuperação** e forneça os endereços de e-mail, se pretender que sejam enviadas notificações de -mail ao concluir o processo de recuperação.

6. No ecrã Resumo, certifique-se de que todos os detalhes estão corretos. Se os detalhes não estiverem corretos, ou se quiser fazer uma alteração, selecione **Back**. Se estiver satisfeito com as definições, selecione **Recuperar** para iniciar o processo de recuperação.

7. O ecrã **Estado de Recuperação** fornece informações sobre a tarefa de recuperação.

## <a name="next-steps"></a>Passos seguintes

[Recuperar dados do Azure Backup Server](./backup-azure-alternate-dpm-server.md)
