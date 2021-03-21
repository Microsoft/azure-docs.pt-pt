---
title: Backup VMs de Solução VMware Azure com Servidor de Backup Azure
description: Configure o seu ambiente de Solução VMware Azure para fazer backup de máquinas virtuais utilizando o Servidor de Backup Azure.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 163065556b7dbc979d68613744ea827c209d9fda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618921"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Backup VMs de Solução VMware Azure com Servidor de Backup Azure

Neste artigo, vamos apoiar as máquinas virtuais VMware (VMs) em execução na Solução VMware Azure com o Azure Backup Server. Em primeiro lugar, percorra minuciosamente [o Microsoft Azure Backup Server para a Solução VMware Azure](set-up-backup-server-for-azure-vmware-solution.md).

Então, vamos percorrer todos os procedimentos necessários para:

> [!div class="checklist"] 
> * Crie um canal seguro para que o Azure Backup Server possa comunicar com servidores VMware através de HTTPS. 
> * Adicione as credenciais de conta ao Azure Backup Server. 
> * Adicione o vCenter ao Servidor de Backup Azure. 
> * Crie um grupo de proteção que contenha os VMware VMs que pretende fazer cópias de segurança, especifique as definições de backup e agende a cópia de segurança.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma ligação segura ao servidor vCenter

Por predefinição, o Azure Backup Server comunica com servidores VMware através de HTTPS. Para configurar a ligação HTTPS, descarregue o certificado da Autoridade de Certificados VMware (CA) e importe-o no Servidor de Backup Azure.

### <a name="set-up-the-certificate"></a>Configurar o certificado

1. No navegador, na máquina Azure Backup Server, insira o URL do cliente web vSphere.

   > [!NOTE] 
   > Se a página VMware **Getting Started** não aparecer, verifique as definições de procuração de ligação e navegador e tente novamente.

1. Na página VMware **Getting Started,** selecione **Descarregue certificados de CA de raiz fidedignos**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere Web Client":::

1. Guarde o ficheiro **download.zip** para a máquina do Servidor de Backup Azure e, em seguida, extrai o seu conteúdo para a pasta **certs,** que contém:

   - Ficheiro de certificado de raiz com uma extensão que começa com uma sequência numerada como .0 e .1.
   - Ficheiro CRL com uma extensão que começa com uma sequência como .r0 ou .r1.

1. Na pasta **certs,** clique com o botão direito no ficheiro do certificado raiz e **selecione Rename** para alterar a extensão para **.crt**.

   O ícone do ficheiro muda para um que representa um certificado de raiz.

1. Clique com o botão direito no certificado de raiz e selecione **o Certificado de Instalação**.

1. No **Certificado De Importação De Assistente**, selecione Máquina **Local** como destino para o certificado, e selecione **Seguinte**.

   ![Página de boas-vindas do feiticeiro](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Se for solicitado, confirme se pretende permitir alterações no computador.

1. Selecione **Coloque todos os certificados na loja seguinte** e selecione Procurar para escolher a loja de **certificados.**

   ![Armazenamento de certificados](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Selecione **As Autoridades de Certificação de Raiz Fidedignas** como pasta de destino e selecione **OK**.

1. Reveja as definições e **selecione Acabamento** para começar a importar o certificado.

   ![Verifique se o certificado está na pasta adequada](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Após a confirmação da importação do certificado, inscreva-se no servidor vCenter para confirmar que a sua ligação está segura.

### <a name="enable-tls-12-on-azure-backup-server"></a>Ativar TLS 1.2 no Servidor de Backup Azure

A VMware 6.7 tinha o TLS ativado como protocolo de comunicação. 

1. Copie as seguintes definições de registo e cole-as no Bloco de Notas. Em seguida, guarde o ficheiro como TLS. REG sem a extensão .txt.

   ```
   
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


## <a name="add-the-account-on-azure-backup-server"></a>Adicione a conta no Azure Backup Server

1. Abra o Servidor de Backup Azure e na consola Azure Backup Server, selecione  >  **Servidores de Produção** de Gestão  >  **Gerencie VMware**.

   ![Consola do servidor de backup Azure](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. Na caixa de diálogo **'Gestão de Credenciais',** selecione **Adicionar**.

   ![Na caixa de diálogo 'Gestão de Credenciais', selecione Add.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. Na caixa de diálogo **Add Credential,** insira um nome e uma descrição para a nova credencial. Especifique o nome de utilizador e a palavra-passe que definiu no servidor VMware.

   > [!NOTE] 
   > Se o servidor VMware e o Servidor de Backup Azure não estiverem no mesmo domínio, especifique o domínio na caixa de **nome do Utilizador.**

   ![Azure Backup Server Adicionar caixa de diálogo credencial](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. **Selecione Adicionar** para adicionar a nova credencial.

   ![A screenshot mostra a caixa de diálogo de gestão de credenciais do servidor de backup Azure com novas credenciais apresentadas.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Adicione o servidor vCenter ao Servidor de Backup Azure

1. Na consola Azure Backup Server, selecione  >  **Management Production Servers**  >  **Add**.

   ![Assistente de adição de servidor de produção aberto](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Selecione **VMware Servers** e selecione **Seguinte**.

   ![Assistente de adição de servidor de produção](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Especificar o endereço IP do vCenter.

   ![Especificar servidor VMware](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Na caixa **de porta SSL,** entre na porta utilizada para comunicar com o vCenter.

   > [!TIP] 
   > A porta 443 é a porta predefinida, mas pode alterá-la se o seu vCenter ouvir numa porta diferente.

1. Na caixa **de credencial de especificação,** selecione a credencial que criou na secção anterior.

1. **Selecione Adicionar** para adicionar o vCenter à lista de servidores e selecione **Seguinte**.

   ![Adicionar servidor VMware e credencial](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Na página **Resumo,** **selecione Adicionar** para adicionar o vCenter ao Servidor de Backup Azure.

   O novo servidor é adicionado imediatamente. VCenter não precisa de um agente.

   ![Adicione o servidor VMware ao Servidor de Backup do Azure](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Na página **'Terminar',** revê as definições e, em seguida, **selecione Fechar**.

   ![Página de acabamento](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Vê o servidor vCenter listado no **Servidor de Produção** com:
   - Digite como **VMware Server** 
   - Estatuto do Agente como **OK** 
   
      Se vir **o Estado do Agente** como **Desconhecido,** selecione **Refresh**.

## <a name="configure-a-protection-group"></a>Configure um grupo de proteção

Os grupos de proteção recolhem vários VMs e aplicam as mesmas definições de retenção de dados e backup a todos os VMs do grupo.

1. Na consola Azure Backup Server, selecione **Protection**  >  **New**.

   ![Abra o assistente do Grupo criar novas proteções](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na página de boas-vindas **do grupo de proteção Criar Nova Proteção,** selecione **Seguinte**.

   ![Criar caixa de diálogo de assistente de novo grupo de proteção](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na página **'Tipo de Grupo de Proteção de Protecção' selecione,** selecione **Servidores** e, em seguida, selecione **Seguinte**. Aparece a página **"Membros do Grupo Select".**

1. Na página **'Selecionar membros do grupo',** selecione as pastas VM (ou VM) que pretende fazer e, em seguida, selecione **Seguinte**.

   > [!NOTE]
   > Quando selecionar uma pasta ou VMs, as pastas no interior dessa pasta também são selecionadas para cópia de segurança. Pode desmarcar pastas ou VMs que não quer fazer recuar. Se um VM ou uma pasta já estiver a ser apoiado, não é possível selecioná-lo, o que garante que não são criados pontos de recuperação duplicados para um VM.

   ![Selecione membros do grupo](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na página 'Selecionar método **de proteção de dados',** insira um nome para as definições de grupo de proteção e proteção. 

1. Descose a proteção de curto prazo para **o Disco,** ative a proteção on-line e, em seguida, selecione **Seguinte**.

   ![Selecionar método de proteção de dados](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Especifique quanto tempo pretende manter os dados no disco.

   - **Intervalo de retenção**: O número de dias em que os pontos de recuperação do disco são mantidos.
   - **Cópia de segurança completa** expressa: Com que frequência são tomadas os pontos de recuperação do disco. Para alterar os horários ou datas quando ocorrem cópias de segurança de curto prazo, **selecione Modificar**.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Especifique os seus objetivos de curto prazo para a proteção baseada em discos":::

1. Na página **de atribuição de armazenamento de disco de revisão,** reveja o espaço de disco previsto para as cópias de segurança VM.

   - As alocações recomendadas em disco baseiam-se na gama de retenção especificada, no tipo de carga de trabalho e no tamanho dos dados protegidos. Faça as alterações necessárias e, em seguida, selecione **Seguinte**.
   - **Tamanho dos dados:** Tamanho dos dados no grupo de proteção.
   - **Espaço em disco:** Quantidade recomendada de espaço em disco para o grupo de proteção. Se pretender modificar esta definição, selecione um espaço ligeiramente maior do que a quantidade que estima que cada fonte de dados cresce.
   - **Detalhes da piscina de armazenamento:** Mostra o estado da piscina de armazenamento, que inclui o tamanho total e restante do disco.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Rever o espaço do disco dado na piscina de armazenamento":::

   > [!NOTE]
   > Em alguns cenários, o tamanho dos dados reportado é superior ao tamanho real do VM. Estamos cientes do problema e estamos a investigá-lo.

1. Na página 'Escolha método **de criação de réplica',** indique como pretende fazer a cópia de segurança inicial e selecione **Seguinte**.

   - O padrão é **automaticamente sobre a rede** e **agora**. Se utilizar o padrão, especifique um tempo fora do pico. Se escolher **Mais tarde,** especifique um dia e hora.
   - Para grandes quantidades de dados ou condições de rede menos ideais, considere replicar os dados offline utilizando suportes amovíveis.

   ![Escolha o método de criação de réplicas](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. Para **obter opções de verificação de consistência**, selecione como e quando automatizar as verificações de consistência e selecione **Next**.

   - Pode executar verificações de consistência quando os dados de réplica se tornam inconsistentes, ou num calendário definido.
   - Se não quiser configurar verificações automáticas de consistência, pode executar uma verificação manual clicando à direita no grupo de proteção **Executar verificação de consistência**.

1. Na página **De Dados de Proteção Online,** selecione as pastas VM ou VM que pretende fazer e, em seguida, selecione **Seguinte**. 

   > [!TIP]
   > Pode selecionar os membros individualmente ou escolher **Select All** para escolher todos os membros.

   ![Especificar dados de proteção on-line](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na página **'Especificar agendamento' de backup online,** indique com que frequência pretende fazer cópias de segurança de armazenamento local para Azure. 

   - Os pontos de recuperação da nuvem para os dados são gerados de acordo com o calendário. 
   - Depois do ponto de recuperação ser gerado, é depois transferido para o cofre dos Serviços de Recuperação em Azure.

   ![Especificar o horário de backup on-line](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na página **'Especificar Política de Retenção Online',** indique quanto tempo pretende manter os pontos de recuperação criados das cópias de segurança para o Azure.

   - Não há limite de tempo para quanto tempo podes manter os dados em Azure.
   - O único limite é que não se pode ter mais de 9.999 pontos de recuperação por instância protegida. Neste exemplo, o caso protegido é o servidor VMware.

   ![Especificar a política de retenção on-line](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na página **Resumo,** reveja as definições e, em seguida, **selecione Criar Grupo**.

   ![Membro do grupo de proteção e resumo de definição](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Monitorar com a consola Azure Backup Server

Depois de configurar o grupo de proteção para apoiar os VMs da Solução VMware Azure, pode monitorizar o estado da tarefa de backup e alertar utilizando a consola Azure Backup Server. Aqui está o que pode monitorizar.

- Na área de tarefa **de monitorização:**
   - Em **Alertas,** pode monitorizar erros, avisos e informações gerais.  Pode ver alertas ativos e inativos e configurar notificações de e-mail.
   - Em **Jobs**, pode visualizar os trabalhos iniciados pelo Azure Backup Server para uma fonte de dados protegida específica ou grupo de proteção. Pode acompanhar o progresso do trabalho ou verificar os recursos consumidos por empregos.
- Na área de **tarefas de Proteção,** pode verificar o estado dos volumes e ações do grupo de proteção. Também pode verificar as definições de configuração, tais como definições de recuperação, atribuição de discos e o calendário de cópias de segurança.
- Na área de **tarefas de Gestão,** pode ver os **separadores Discos, Online** e **Agentes** para verificar o estado dos discos na piscina de armazenamento, o registo no Azure e o estado do agente DPM implantado.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Monitorize o estado dos trabalhos de backup no Azure Backup Server":::

## <a name="restore-vmware-virtual-machines"></a>Restaurar máquinas virtuais VMware

Na Consola de Administrador do Servidor de Backup Azure, existem duas formas de encontrar dados recuperáveis. Pode pesquisar ou navegar. Quando recuperar dados, pode ou não querer restaurar dados ou um VM no mesmo local. Por esta razão, o Azure Backup Server suporta três opções de recuperação para cópias de segurança VMware VM:

- **Recuperação original da localização (OLR)**: Utilize o OLR para restaurar um VM protegido na sua localização original. Só pode restaurar um VM na sua localização original se não forem adicionados ou eliminados discos desde que a cópia de segurança ocorreu. Se os discos forem adicionados ou eliminados, deve utilizar a recuperação alternativa da localização.
- **Recuperação alternativa da localização (ALR)**: Utilize quando o VM original estiver em falta, ou não quer perturbar o VM original. Forneça a localização de um anfitrião ESXi, piscina de recursos, pasta e a loja de dados de armazenamento e caminho. Para ajudar a diferenciar o VM restaurado do VM original, o Azure Backup Server anexa *"-Recuperado"* ao nome do VM.
- **Recuperação individual da localização do ficheiro (ILR)**: Se o VM protegido for um VM do Servidor do Windows, ficheiros ou pastas individuais dentro do VM podem ser recuperados utilizando a capacidade ILR do Servidor de Backup Azure. Para recuperar ficheiros individuais, consulte o procedimento mais tarde neste artigo. Restaurar um ficheiro individual a partir de um VM está disponível apenas para pontos de recuperação do Windows VM e discos.

### <a name="restore-a-recovery-point"></a>Restaurar um ponto de recuperação

1. Na consola de administrador do servidor de backup Azure, selecione a vista **Recovery.** 

1. Utilizando o painel **de navegação,** navegue ou filtre para encontrar o VM que pretende recuperar. Depois de selecionar um VM ou uma pasta, os pontos de recuperação **para painel de visualização dos pontos de recuperação disponíveis.

   ![Pontos de recuperação disponíveis](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. Nos **pontos de recuperação do** painel, selecione uma data quando um ponto de recuperação foi tomado. As datas do calendário em negrito têm pontos de recuperação disponíveis. Alternadamente, pode clicar à direita no VM e selecionar **Mostrar todos os pontos de recuperação** e, em seguida, selecionar o ponto de recuperação da lista.

   > [!NOTE] 
   > Para uma proteção a curto prazo, selecione um ponto de recuperação baseado em disco para uma recuperação mais rápida. Após o termo dos pontos de recuperação de curto prazo, vê apenas pontos de recuperação **online** para recuperar.

1. Antes de recuperar de um ponto de recuperação on-line, certifique-se de que o local de preparação contém espaço livre suficiente para alojar o tamanho total não comprimido do VM que pretende recuperar. A localização de paragem pode ser visualizada ou alterada executando o Assistente de **Definições de Assinatura Configure**.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Definições de dobragem de recuperação de servidor de backup Azure":::

1. Selecione **Recuperar** para abrir o **Assistente de Recuperação**.

   ![Assistente de recuperação, página de seleção de recuperação de revisão](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Selecione **Seguinte** para ir ao ecrã **Desempecar Opções de Recuperação.** Selecione **Next** novamente para ir ao ecrã **Select Recovery Type.** 

   > [!NOTE]
   > As cargas de trabalho da VMware não suportam o estrangulamento da largura de banda da rede.

1. Na página **Select Recovery Type,** ou recupere para a instância original ou para uma nova localização.

   - Se escolher **Recuperar para instância original,** não precisa de fazer mais escolhas no assistente. Os dados relativos à instância original são utilizados.
   - Se escolher **Recuperar como máquina virtual em qualquer anfitrião,** em seguida, no ecrã De destino **especifique,** forneça as informações para **anfitrião ESXi,** **Pool de Recursos,** **Pasta** e **Caminho**.

   ![Selecione página tipo de recuperação](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Na página **Resumo,** reveja as definições e selecione **Recuperar** para iniciar o processo de recuperação. 

   O ecrã **de estado de recuperação** mostra a progressão da operação de recuperação.

### <a name="restore-an-individual-file-from-a-vm"></a>Restaurar um ficheiro individual a partir de um VM

Pode restaurar ficheiros individuais a partir de um ponto de recuperação de VM protegido. Esta funcionalidade só está disponível para VMs do Windows Server. Restaurar ficheiros individuais é semelhante para restaurar todo o VM, exceto navegar no VMDK e encontrar os ficheiros que deseja antes de iniciar o processo de recuperação. 

> [!NOTE]
> Restaurar um ficheiro individual a partir de um VM está disponível apenas para pontos de recuperação do Windows VM e discos.

1. Na consola de administrador do servidor de backup Azure, selecione a vista **Recovery.**

1. Utilizando o painel **de navegação,** navegue ou filtre para encontrar o VM que pretende recuperar. Depois de selecionar um VM ou uma pasta, os pontos de recuperação **para painel de visualização dos pontos de recuperação disponíveis.

   ![Pontos de recuperação disponíveis](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. Nos **pontos de recuperação do** painel, utilize o calendário para selecionar a data que contém os pontos de recuperação desejados. Dependendo da configuração da política de backup, as datas podem ter mais do que um ponto de recuperação. 

1. Depois de selecionar o dia em que o ponto de recuperação foi tomado, certifique-se de que escolhe o tempo correto **de recuperação**. 

   > [!NOTE]
   > Se a data selecionada tiver vários pontos de recuperação, escolha o seu ponto de recuperação selecionando-o no menu de entrega do tempo de **recuperação.** 

   Depois de escolher o ponto de recuperação, a lista de itens recuperáveis aparece no painel **path.**

1. Para encontrar os ficheiros que pretende recuperar, no painel **Path,** clique duas vezes no item na coluna **Item Recuperável** para o abrir. Em seguida, selecione o ficheiro ou as pastas que pretende recuperar. Para selecionar vários itens, selecione a tecla **Ctrl** enquanto seleciona cada item. Utilize o painel **Path** para pesquisar a lista de ficheiros ou pastas que aparecem na coluna **Item Recuperável.**
    
   > [!NOTE]
   > **A lista de pesquisa abaixo** não procura sub-dobradeiras. Para pesquisar através de sub-14, clique duas vezes na pasta. Utilize o botão **Up** para passar de uma pasta para crianças para a pasta dos pais. Pode selecionar vários itens (ficheiros e pastas), mas devem estar na mesma pasta dos pais. Não é possível recuperar itens de várias pastas no mesmo trabalho de recuperação.

   ![Seleção de recuperação de revisão](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Quando tiver selecionado os itens para recuperação, na fita da ferramenta Consola de Administrador, selecione **Recuperar** para abrir o **Assistente de Recuperação**. No **Assistente de Recuperação,** o ecrã **de Seleção de Recuperação** de Revisão mostra os itens selecionados a recuperar.

1. No ecrã **'Especificar Opções de Recuperação',** faça um dos seguintes passos:

   - Selecione **Modificar** para ativar o estrangulamento da largura de banda da rede. Na caixa de diálogo **do Acelerador,** selecione **Ativar o acelerador de utilização** da largura de banda da rede para o ligar. Uma vez ativado, configurar as **Definições** e a **Programação de Trabalho**.
   - Selecione **Seguinte** para deixar o estrangulamento da rede desativado.

1. No ecrã **Select Recovery Type,** selecione **Seguinte**. Só é possível recuperar os seus ficheiros ou pastas numa pasta de rede.

1. No ecrã **'Destino Específico',** selecione **Procurar** para encontrar uma localização de rede para os seus ficheiros ou pastas. O Azure Backup Server cria uma pasta onde todos os itens recuperados são copiados. O nome da pasta tem o prefixo MABS_day mês. Quando seleciona um local para os ficheiros ou pasta recuperados, os detalhes dessa localização são fornecidos.

   ![Especificar a localização para recuperar ficheiros](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. No ecrã **'Especificar Opções de Recuperação',** escolha qual a definição de segurança a aplicar. Pode optar por modificar o estrangulamento de utilização da largura de banda da rede, mas o estrangulamento é desativado por defeito. Além disso, **a RECUPERAção** e **Notificação** da SAN não estão ativadas.

1. No ecrã **Resumo,** reveja as definições e selecione **Recuperar** para iniciar o processo de recuperação. O ecrã **de estado de recuperação** mostra a progressão da operação de recuperação.

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu o backup dos VMs da Solução VMware Azure com o Azure Backup Server, talvez queira saber mais sobre: 

- [Resolução de problemas ao configurar cópias de segurança no Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md).
- [Gestão do ciclo de vida da Azure VMware Solution VMs](lifecycle-management-of-azure-vmware-solution-vms.md).
