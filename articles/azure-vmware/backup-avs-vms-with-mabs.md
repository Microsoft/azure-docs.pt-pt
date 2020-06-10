---
title: Backup Azure VMware Solution (AVS) VMs com Microsoft Azure Backup Server
description: Configure o seu ambiente Azure VMware Solution (AVS) para fazer backup de máquinas virtuais utilizando o Microsoft Azure Backup Server.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 16941753d614bbfacbc63e4ae518986901153662
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612762"
---
# <a name="back-up-avs-vms-with-microsoft-azure-backup-server"></a>Faça backup VMs com Microsoft Azure Backup Server

Neste artigo, passamos pelos procedimentos para apoiar as máquinas virtuais VMware que executam na Azure VMware Solution (AVS) utilizando o Azure Backup Server. Antes de começar, certifique-se de que passa minuciosamente pelo [Servidor de Backup do Microsoft Azure para AVS](set-up-mabs-for-avs.md). 

Depois, percorremos todos os procedimentos necessários para:

> [!div class="checklist"] 
> * Crie um canal seguro para que o Azure Backup Server possa comunicar com servidores VMware através de HTTPS. 
> * Adicione as credenciais de conta ao Azure Backup Server. 
> * Adicione o vCenter ao Servidor de Backup Azure. 
> * Crie um grupo de proteção que contenha os VMware VMs que pretende fazer cópias de segurança, especifique as definições de backup e agende a cópia de segurança.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma ligação segura ao servidor vCenter

Por predefinição, o Azure Backup Server comunica com servidores VMware através de HTTPS. Para configurar a ligação HTTPS, descarregue o certificado VMware Certificate Authority (CA) e importe-o no Servidor de Backup Azure.

### <a name="set-up-the-certificate"></a>Configurar o certificado

1. No navegador, no Azure Backup Server, insira o URL do cliente web vSphere.

   > [!NOTE] 
   > Se a página VMware Getting Started não aparecer, verifique as definições de procuração de ligação e navegador e tente novamente.

1. Na página VMware Getting Started, clique em **Baixar certificados de RAIZ fidedignos**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere Web Client":::

1. Guarde o ficheiro **download.zip** para a máquina do Servidor de Backup Azure e, em seguida, extrai o seu conteúdo para a pasta **certs,** que contém o seguinte:

   - Ficheiro de certificado de raiz com uma extensão que começa com uma sequência numerada como .0 e .1.

   - O ficheiro CRL tem uma extensão que começa com uma sequência como .r0 ou .r1.

1. Na pasta **certs,** clique com o botão direito no ficheiro do certificado raiz e **selecione Rename** para alterar a extensão para **.crt**.

   O ícone do ficheiro muda para um que representa um certificado de raiz.

1. Clique com o botão direito no certificado de raiz e selecione **o Certificado de Instalação**.

1. No **Certificado De Importação De Assistente**, selecione Máquina **Local** como destino para o certificado, e clique em **Seguinte**.

   ![Bem-vindo ao Feiticeiro](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Se for solicitado, confirme se pretende permitir alterações no computador.    

1. Selecione **Coloque todos os certificados na loja seguinte**e clique em procurar para escolher a loja de **certificados.**

   ![Armazenamento de certificados](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Selecione **As Autoridades de Certificação de Raiz Fidedigna como** a pasta de destino e clique em **OK**.

1. Reveja as definições e clique **em Terminar** para começar a importar o certificado.

   ![Verifique se o certificado está na pasta adequada](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Após a confirmação da importação do certificado, inscreva-se no servidor vCenter para confirmar que a sua ligação está segura.

### <a name="enable-tls-12-on-azure-backup-server"></a>Ativar TLS 1.2 no Servidor de Backup Azure

VMWare 6.7 em diante tinha TLS ativado como protocolo de comunicação. 

1. Copie as seguintes definições de registo e cole-as no Bloco de Notas e guarde o ficheiro como TLS. REG sem a extensão .txt.

   ```text
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. Clique com o botão direito no TLS. Ficheiro REG e **selecione Merge** ou **Open** para adicionar as definições ao registo.

## <a name="add-the-provisioning-ip-address-for-avs-esxi-hosts-on-azure-backup-server"></a>Adicione o endereço IP de provisionamento para anfitriões AVS ESXi no Azure Backup Server

Durante a pré-visualização, o AVS não resolve o hospedeiro ESX a partir da máquina virtual implantada na rede virtual. Terá de realizar passos adicionais para adicionar a entrada de ficheiro do anfitrião na máquina virtual do Azure Backup Server.

### <a name="identify-ip-address-for-esxi-hosts"></a>Identificar endereço IP para anfitriões ESXi

1. Abra o navegador e inscreva-se nos URLs vCenter. 

   > [!TIP]
   > Pode encontrar os URLs no [Connect to the local vCenter do seu](tutorial-access-private-cloud.md#connect-to-the-local-vcenter-of-your-private-cloud) artigo em nuvem privada.

1. No vSphere Client, selecione o cluster que planeia ativar a cópia de segurança.

   :::image type="content" source="media/avs-backup/vsphere-client-select-host.png" alt-text="VSphere Client - Selecione anfitrião":::

1. Selecione **Configure**  >  os adaptadores VMKernel**de rede configurados**, e na lista de  >  **VMKernel adapters**dispositivos, identifique o adaptador de rede que tem a **função de Provisionamento** ativado e, em seguida, tome nota do endereço **IP** e do nome de anfitrião ESXi.

   :::image type="content" source="media/avs-backup/vmkernel-adapters-provisioning-enabled.png" alt-text="Adaptadores VMKernel - dispositivos habilitados para provisionamento":::

1. Repita o passo anterior para cada anfitrião ESXi em cada cluster que planeia ativar a cópia de segurança.

### <a name="update-the-host-file-on-the-azure-backup-server"></a>Atualize o ficheiro do anfitrião no Servidor de Backup Azure

1. Abra **o Bloco de Notas** como administrador.

2. Selecione **'Ficheiro > Aberto** e procure c:\Windows\System32\Drivers\etc\hosts.

3. Adicione a entrada para cada anfitrião ESXi juntamente com o Endereço IP que identificou na secção acima.

4. Guarde as alterações e feche o Bloco de Notas.

## <a name="add-the-account-on-azure-backup-server"></a>Adicione a conta no Azure Backup Server


1. Abra o Servidor de Backup Azure e na **Management**consola Azure Backup Server, selecione  >  **Servidores de Produção**de Gestão  >  **Gerencie VMware**.

   ![Consola do servidor de backup Azure](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. Na caixa de diálogo **'Gestão de Credenciais',** clique em **Adicionar**.

   ![Azure Backup Servidor Gerencie caixa de diálogo de credenciais](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. In **Add Credential,** insira um nome e uma descrição para a nova credencial, e especifique o nome de utilizador e a palavra-passe que definiu no servidor VMware.

   > [!NOTE] 
   > Se o servidor VMware e o Servidor de Backup Azure não estiverem no mesmo domínio, especifique o domínio no nome de utilizador.

   ![Azure Backup Server Adicionar caixa de diálogo credencial](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Clique **em Adicionar** para adicionar a nova credencial.

   ![Azure Backup Servidor Gerencie caixa de diálogo de credenciais](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Adicione o servidor vCenter ao servidor de backup do Azure

1. Na consola Azure Backup **Management**Server, clique em  >  **Servidores de Produção**de Gestão  >  **Add**.

   ![Assistente de adição de servidor de produção aberto](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Selecione **VMware Servers** e clique em **Seguinte**.

   ![Assistente de adição de servidor de produção](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Especificar o endereço IP do vCenter.

   ![Especificar servidor VMware](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Na **porta SSL,** entre na porta utilizada para comunicar com o vCenter.

   > [!TIP] 
   > A porta 443 é a porta predefinida, mas pode alterá-la se o seu vCenter ouvir numa porta diferente.

1. Em **Especificar Credencial,** selecione a credencial que criou na secção anterior.

1. Clique em **Adicionar** para adicionar o vCenter à lista de servidores e clique em **Seguinte**.

   ![Adicionar servidor VMWare e credencial](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Na página **Resumo,** clique **em Adicionar** para adicionar o vCenter ao Servidor de Backup Azure.

   O novo servidor é adicionado imediatamente; vCenter não precisa de um agente.

   ![Adicione o servidor VMware ao Servidor de Backup do Azure](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Na página **'Terminar',** revê as definições e, em seguida, clique em **Fechar**.

   ![Página de acabamento](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Deverá ver o servidor vCenter listado no Servidor de Produção com o tipo como VMware Server e Agent Status como *OK*. Se vir o estado do agente como *desconhecido,* clique em **Refresh**.

## <a name="configure-a-protection-group"></a>Configure um grupo de proteção

Os grupos de proteção recolhem vários VMs e aplicam as mesmas definições de retenção de dados e backup a todos os VMs do grupo.

1. Na consola Azure Backup Server, clique em **Proteção**  >  **Nova**.

   ![Abra o assistente do Grupo criar novas proteções](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na página de boas-vindas **do grupo de proteção Criar Nova Proteção,** clique em **Seguinte**.

   ![Criar caixa de diálogo de assistente de novo grupo de proteção](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na página do **tipo do grupo Select Protection,** selecione **Servidores** e, em seguida, clique em **Seguinte**. Aparece a página **de membros do grupo Select.**

1. Nos **membros do grupo Select**, selecione as pastas VM (ou VM) que pretende fazer. Em seguida, clique em **Seguinte**.

   > [!NOTE]
   > Quando selecionar uma pasta ou VMs ou pastas no interior dessa pasta também são selecionadas para cópia de segurança. Pode desmarcar pastas ou VMs que não quer fazer recuar. Se um VM ou uma pasta já estiver a ser apoiado, não é possível selecioná-lo, o que garante que não são criados pontos de recuperação duplicados para um VM.

   ![Selecione membros do grupo](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na página 'Selecionar método **de proteção de dados',** insira um nome para as definições de grupo de proteção e proteção. 

1. Desa estaca a proteção de curto prazo para **o Disco,** ative a proteção on-line e, em seguida, clique em **Seguinte**.

   ![Selecionar método de proteção de dados](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Especifique quanto tempo pretende manter os dados no disco.

   - **Intervalo de Retenção** - número de dias de recuperação do disco são mantidos.
   - **Express Full Backup** - com que frequência são tomadas os pontos de recuperação do disco. Para alterar os horários/datas quando ocorrem cópias de segurança de curto prazo, clique em **Modificar**.

   :::image type="content" source="media/avs-backup/new-protection-group-specify-short-term-goals.png" alt-text="Especifique os seus objetivos de curto prazo para a proteção baseada em discos":::

1. Na **Atribuição de Discos de Revisão,** reveja o espaço do disco previsto para as cópias de segurança VM.

   - As alocações recomendadas em disco baseiam-se na gama de retenção especificada, no tipo de carga de trabalho e no tamanho dos dados protegidos. Faça as alterações necessárias e, em seguida, clique em **Seguinte**.

   - **Tamanho dos dados:** Tamanho dos dados no grupo de proteção.

   - **Espaço em disco:** Quantidade recomendada de espaço em disco para o grupo de proteção. Se quiser modificar esta definição, deverá alocar um espaço total ligeiramente maior do que a quantidade que estima que cada fonte de dados cresce.

   - **Detalhes da piscina de armazenamento:** Mostra o estado da piscina de armazenamento, incluindo o tamanho total e restante do disco.

   :::image type="content" source="media/avs-backup/review-disk-allocation.png" alt-text="Rever o espaço do disco atribuído na piscina de armazenamento":::

   > [!NOTE]
   > Em alguns cenários, o tamanho de dados reportado é superior ao tamanho real de VM. Estamos cientes da questão e estamos atualmente a investigá-lo.

1. Na página 'Escolha método **de criação de réplica',** indique como pretende fazer a cópia de segurança inicial e clique em **Seguinte**.

   - O padrão é **automaticamente sobre a rede** e **agora**.   Se utilizar o padrão, recomendamos que especifique um tempo fora do pico. Escolha **Mais tarde** e especifique um dia e hora.

   - Para grandes quantidades de dados ou condições de rede menos ideais, considere replicar os dados offline utilizando suportes amovíveis.

   ![Escolha o método de criação de réplicas](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. Em **Opções de Verificação de Consistência**, selecione como e quando automatizar as verificações de consistência e clique em **Seguinte**.

   - Pode executar verificações de consistência quando os dados de réplica se tornam inconsistentes, ou num calendário definido.

   - Se não quiser configurar verificações automáticas de consistência, pode executar uma verificação manual clicando à direita no grupo de proteção **Executar verificação de consistência**.

1. Na página **De Dados de Proteção Online,** selecione as pastas VM ou VM que pretende fazer e, em seguida, clique em **Seguinte**. 

   > [!TIP]
   > Pode selecionar os membros individualmente ou clicar **em Selecionar Todos** para escolher todos os membros.

   ![Especificar dados de proteção on-line](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na página **'Especificar agendamento' de cópia de segurança on-line,** indique quantas vezes pretende fazer cópias de segurança do armazenamento local para o Azure e clique em **Seguinte**. 

   - Os pontos de recuperação da nuvem para os dados são gerados de acordo com o calendário. 

   - Depois do ponto de recuperação ser gerado, é depois transferido para o cofre dos Serviços de Recuperação em Azure.

   ![Especificar o horário de backup on-line](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na página **'Especificar Política de Retenção Online',** indique quanto tempo pretende manter os pontos de recuperação que são criados a partir das cópias de segurança diárias/semanais/mensais/ano para Azure e, em seguida, clique em **Seguinte**.

   - Não há limite de tempo para quanto tempo podes manter os dados em Azure.

   - O único limite é que não se pode ter mais de 9999 pontos de recuperação por instância protegida. Neste exemplo, o caso protegido é o servidor VMware.

   ![Especificar a política de retenção on-line](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na página **Resumo,** reveja as definições e clique em **Criar Grupo**.

   ![Membro do grupo de proteção e resumo de definição](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-azure-backup-server-console"></a>Monitor com consola Azure Backup Server

Uma vez configurado o grupo de proteção para fazer backup de VMs AVS, pode monitorizar o estado do trabalho de backup e alertar utilizando a consola Azure Backup Server. Aqui está o que pode monitorizar.

- No separador Alertas no painel de monitorização, pode monitorizar erros, avisos e informações gerais para um grupo de proteção, para um computador protegido específico ou por gravidade da mensagem. Pode ver alertas ativos e inativos e configurar notificações por e-mail

- No separador Jobs no painel de monitorização, pode visualizar os trabalhos iniciados pelo Azure Backup Server para um grupo específico de fonte de dados protegido ou grupo de proteção. Pode acompanhar o progresso do trabalho ou verificar os recursos consumidos por empregos.

- Na área de **tarefas de Proteção,** pode verificar o estado dos volumes e ações do grupo de proteção e verificar as definições de configuração, tais como definições de recuperação, atribuição de discos e o calendário de cópias de segurança.

- Na área de **tarefas de Gestão,** pode ver o separador **Discos, Online**e **Agentes** para verificar o estado dos discos na piscina de armazenamento, o registo no Azure e o estado do agente DPM implantado.

:::image type="content" source="media/avs-backup/monitor-backup-jobs-in-mabs.png" alt-text="Monitorize o estado dos trabalhos de backup no Azure Backup Server":::

## <a name="restore-vmware-virtual-machines"></a>Restaurar máquinas virtuais VMware

Na Consola de Administrador do Servidor de Backup Azure, existem duas formas de encontrar dados recuperáveis - pesquisar ou navegar. Ao recuperar dados, pode ou não querer restaurar dados ou um VM no mesmo local. Por esta razão, o Azure Backup Server suporta três opções de recuperação para cópias de segurança VMware VM:

- **Recuperação original da localização (OLR)** - Utilize o OLR para restaurar um VM protegido na sua localização original. Só pode restaurar um VM na sua localização original se não tiverem sido adicionados ou eliminados discos desde que a cópia de segurança ocorreu. Se os discos tiverem sido adicionados ou eliminados, deve utilizar a recuperação alternativa da localização.

- **Recuperação de localização alternativa (ALR)** - Quando o VM original estiver em falta, ou não quiser perturbar o VM original, recupere o VM para um local alternativo. Para recuperar um VM para um local alternativo, deve fornecer a localização de um anfitrião ESXi, piscina de recursos, pasta e a loja de dados de armazenamento e caminho. Para ajudar a diferenciar o VM restaurado do VM original, o Azure Backup Server anexa "-Recuperado" ao nome do VM.

- **Recuperação individual da localização do ficheiro (ILR)** - Se o VM protegido for um VM do Servidor do Windows, ficheiros/pastas individuais dentro do VM podem ser recuperados utilizando a capacidade ILR do Azure Backup Server. Para recuperar ficheiros individuais, consulte o procedimento mais tarde neste artigo. Restaurar um ficheiro individual a partir de um VM está disponível apenas para Pontos de Recuperação do Windows VM e Discos.

### <a name="restore-a-recovery-point"></a>Restaurar um ponto de recuperação

1. Na consola de administrador do servidor de backup Azure, clique na visualização de Recuperação. 

1. Utilizando o painel de navegação, navegue ou filtre para encontrar o VM que pretende recuperar. Uma vez selecionado um VM ou uma pasta, os pontos de recuperação do painel de pontos mostram os pontos de recuperação disponíveis.

   ![Pontos de recuperação disponíveis](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. Nos **pontos de recuperação para** o campo, utilize os menus de calendário e suspensos para selecionar uma data quando um ponto de recuperação foi tomado. As datas do calendário em negrito têm pontos de recuperação disponíveis. Alternadamente, pode clicar à direita no VM e selecionar **mostrar todos os pontos de recuperação** e, em seguida, selecionar o ponto de recuperação da lista.

   > [!NOTE] 
   > Para uma proteção a curto prazo, selecione um ponto de recuperação baseado em disco para uma recuperação mais rápida. Após o termo dos pontos de recuperação de curto prazo, só se vê pontos de recuperação online para recuperar.

1. Antes de recuperar de um ponto de recuperação on-line, certifique-se de que o local de preparação contém espaço livre suficiente para alojar o tamanho total não comprimido do VM que pretende recuperar. A localização de paragem pode ser visualizada/alterada executando o assistente de definições de **configuração**.

   :::image type="content" source="media/avs-backup/mabs-recovery-folder-settings.png" alt-text="Definições de dobragem de recuperação de servidor de backup Azure":::

1. Clique em **Recuperar** para abrir o **Assistente de Recuperação**.

   ![Assistente de recuperação, seleção de recuperação de revisão](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Clique **em seguir** para avançar para o ecrã de **Opções de Recuperação e** clique em **Seguinte** novamente para avançar para o ecrã do **tipo 'Recuperar' Select.** 

   > [!NOTE]
   > As cargas de trabalho VMware não suportam o estrangulamento da largura de banda da rede.

1. Na página **Select Recovery Type,** escolha se deve recuperar para a instância original ou para uma nova localização e, em seguida, clique em **Seguinte**.

   - Se escolher **Recuperar para instância original,** não precisa de fazer mais escolhas no assistente. Os dados relativos à instância original são utilizados.

   - Se escolher **Recuperar como máquina virtual em qualquer anfitrião,** em seguida, no ecrã De destino **especifique,** forneça as informações para **anfitrião ESXi, Pool de Recursos, Pasta**e **Caminho**.

   ![Selecione tipo de recuperação](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Na página **Resumo,** reveja as definições e clique em **Recuperar** para iniciar o processo de recuperação. 

   O ecrã de estado de recuperação mostra a progressão da operação de recuperação.

### <a name="restore-an-individual-file-from-a-vm"></a>Restaurar um ficheiro individual a partir de um VM

Pode restaurar ficheiros individuais a partir de um ponto de recuperação de VM protegido. Esta funcionalidade só está disponível para VMs do Windows Server. Restaurar ficheiros individuais é semelhante para restaurar todo o VM, exceto navegar no VMDK e encontrar os ficheiros que deseja antes de iniciar o processo de recuperação. 

> [!NOTE]
> Restaurar um ficheiro individual a partir de um VM está disponível apenas para Pontos de Recuperação do Windows VM e Discos.

1. Na consola de administrador do servidor de backup Azure, clique na **visualização de Recuperação.**

1. Utilizando o painel **de navegação,** navegue ou filtre para encontrar o VM que pretende recuperar. Uma vez selecionado um VM ou uma pasta, os pontos de recuperação do painel de pontos mostram os pontos de recuperação disponíveis.

   ![Pontos de recuperação disponíveis](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. Nos **Pontos de Recuperação do** painel, utilize o calendário para selecionar a data que contém o ou os pontos de recuperação pretendidos. Dependendo da configuração da política de backup, as datas podem ter mais do que um ponto de recuperação. 

1. Depois de ter selecionado o dia em que o ponto de recuperação foi tomado, certifique-se de que escolheu o tempo correto **de recuperação**. 

   > [!NOTE]
   > Se a data selecionada tiver vários pontos de recuperação, escolha o seu ponto de recuperação selecionando-o no menu de entrega do tempo de recuperação. 

   Depois de escolher o ponto de recuperação, a lista de itens recuperáveis aparece no painel **path.**  

1. Para encontrar os ficheiros que pretende recuperar, no painel **Path,** clique duas vezes no item na coluna **de item recuperável** para o abrir e selecione o ficheiro ou pastas que pretende recuperar. Para selecionar vários itens, prima a tecla **Ctrl** enquanto seleciona cada item. Utilize o painel **'Caminho'** para pesquisar a lista de ficheiros ou pastas que aparecem na coluna **Item Recuperável.**
    
   > [!NOTE]
   > **A lista de pesquisa abaixo** não procura subfolders. Para pesquisar através de sub-14, clique duas vezes na pasta. Utilize o botão **Up** para passar de uma pasta para crianças para a pasta dos pais. Pode selecionar vários itens (ficheiros e pastas), mas devem estar na mesma pasta dos pais. Não é possível recuperar itens de várias pastas no mesmo trabalho de recuperação.

   ![Seleção de Recuperação de Revisão](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Quando tiver selecionado os itens para recuperação, na fita da ferramenta Consola de Administrador, clique em **Recuperar** para abrir o **Assistente de Recuperação**. No Assistente de Recuperação, o ecrã **de Seleção de Recuperação** de Revisão mostra os itens selecionados a recuperar.

1. No ecrã **De Especificar Opções de Recuperação,** faça uma das seguintes:

   - Clique **em Modificar** para ativar o estrangulamento da largura de banda da rede.  No diálogo do acelerador, selecione **Ativar o acelerador de utilização** da largura de banda da rede para o ligar. Uma vez ativado, configurar as **Definições** e a **Programação de Trabalho**.
    
   - Clique **ao lado** para deixar o estrangulamento da rede desativado.

1. No ecrã **Select Recovery Type,** clique em **Seguinte**. Só pode recuperar os ficheiros ou pastas para uma pasta de rede.

1. No ecrã **'Destino Específico',** clique em **procurar** para encontrar uma localização de rede para os seus ficheiros ou pastas. O Azure Backup Server cria uma pasta onde todos os itens recuperados são copiados. O nome da pasta tem o prefixo, MABS_day mês. Quando seleciona uma localização para os ficheiros ou pasta recuperados, são fornecidos os detalhes desse local (Destino, destino e espaço disponível).

   ![Especificar a localização para recuperar ficheiros](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. No ecrã **'Especificar Opções de Recuperação',** escolha qual a definição de segurança a aplicar. Pode optar por modificar o estrangulamento de utilização da largura de banda da rede, mas o estrangulamento é desativado por defeito. Além disso, **a RECUPERAção** e **Notificação da** SAN não estão ativadas.

1. No ecrã **Resumo,** reveja as definições e clique em **Recuperar** para iniciar o processo de recuperação. O ecrã **de estado de recuperação** mostra a progressão da operação de recuperação.

## <a name="next-steps"></a>Próximos passos

Para problemas de resolução de problemas ao configurar backups, reveja o guia de resolução de problemas para O Servidor de Backup Azure.



> [!div class="nextstepaction"]
> [Guia de resolução de problemas para o servidor de backup do Azure](../backup/backup-azure-mabs-troubleshoot.md)
