---
title: Fazer uma cópia de segurança de VMs de VMware no servidor de cópia de segurança do Azure
description: Utilize o Azure Backup Server para fazer uma cópia de segurança de VMs de VMware em execução num servidor VMware vCenter/ESXi.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: 8a660de0502a6ab215d3a23615f6a53813f1695e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649208"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Fazer uma cópia de segurança de VMs de VMware no servidor de cópia de segurança do Azure

Este artigo explica como fazer uma cópia de segurança de VMs de VMware em execução em anfitriões do VMware ESXi/vCenter Server no Azure com o servidor de cópia de segurança do Azure.

Este artigo explica como:

- Configure um canal seguro para que o servidor de cópia de segurança do Azure pode comunicar com servidores do VMware através de HTTPS.
- Configure uma conta de VMware que utiliza o Azure Backup Server para aceder ao servidor VMware.
- Adicione as credenciais de conta para o Azure Backup.
- Adicione o vCenter ou o servidor ESXi ao servidor de cópia de segurança do Azure.
- Configure um grupo de proteção que contém as VMs do VMware que pretende criar cópias de segurança, especifique as definições de cópia de segurança e agendar a cópia de segurança.

## <a name="before-you-start"></a>Antes de começar
- Certifique-se de que está a executar uma versão do vCenter/ESXi que é suportada para cópia de segurança - versões 6.5, 6.0 e 5.5.
- Certifique-se de que configurou a servidor de cópia de segurança do Azure. Caso contrário, [fazê-lo](backup-azure-microsoft-azure-backup.md) antes de começar. Deve ter o servidor de cópia de segurança do Azure com as atualizações mais recentes.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma ligação segura ao vCenter Server

Por predefinição, o servidor de cópia de segurança do Azure comunica com servidores do VMware através de HTTPS. Para configurar a ligação HTTPS, transfira o certificado de autoridade de certificado de VMware (AC) e importe-o no servidor de cópia de segurança do Azure.


### <a name="before-you-start"></a>Antes de começar

- Se não pretender utilizar HTTPS, pode [desativar a definição predefinida](backup-azure-backup-server-vmware.md).
- Normalmente, ligar a partir de um browser no computador do servidor de cópia de segurança do Azure para o servidor do vCenter/ESXi usando o vSphere Web Client. Na primeira vez, fazê-lo a ligação não é segura e irá mostrar o seguinte.
- É importante compreender a forma como o servidor de cópia de segurança do Azure processa as cópias de segurança.
    - Como primeiro passo servidor de cópia de segurança do Azure cria uma cópia de segurança de dados para o armazenamento de disco local. O servidor de cópia de segurança do Azure utiliza um agrupamento de armazenamento, um conjunto de discos e volumes nos quais o servidor de cópia de segurança do Azure armazena pontos de recuperação de disco para os respetivos dados protegidos. O agrupamento de armazenamento pode ser ligado diretamente ao armazenamento (DAS), um dispositivo de armazenamento de iSCSI ou SAN de canal de Fibra ou SAN. É importante certificar-se de que tem armazenamento suficiente na cópia local de cópia de segurança dos seus dados de VM de VMware.
    - O servidor de cópia de segurança do Azure, em seguida, efetua cópias de segurança do armazenamento de disco local para o Azure.
    - [Obtenha ajuda](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) para descobrir o quanto do espaço de armazenamento necessário. As informações são para o DPM, mas também poderão ser usadas para o servidor de cópia de segurança do Azure.

### <a name="set-up-the-certificate"></a>Configurar o certificado

Configure um canal seguro da seguinte forma:

1. No browser no servidor de cópia de segurança do Azure, introduza o URL de cliente da Web do vSphere. Se a página de início de sessão não aparecer, verifique as definições de proxy de ligação e o navegador.

    ![vSphere Web Client](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na página de início de sessão do cliente Web do vSphere, clique em **Download fidedigna de certificados de AC de raiz**.

    ![Transferir o certificado de AC de raiz fidedigna](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Um ficheiro denominado **transferir** é transferida. Dependendo do seu browser, receberá uma mensagem que perguntar se pretende abrir ou guardar o ficheiro.

    ![Transferir o certificado de AC](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Guarde o ficheiro na máquina do servidor de cópia de segurança do Azure com uma extensão. zip.

5. Com o botão direito **download.zip** > **extrair todos os**. O ficheiro. zip extrai os respetivos conteúdos para o **certificados** pasta, que contém:
    - O ficheiro de certificado de raiz com uma extensão que começa com uma sequência numerada como.0 e.1.
    - O ficheiro CRL tem uma extensão que começa com uma seqüência como .r0 ou .r1. O ficheiro CRL está associado um certificado.

    ![Certificados transferidos](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Na **certificados** pasta, clique com o botão direito no ficheiro de certificado de raiz > **mudar o nome**.

    ![Mudar o nome do certificado de raiz ](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. Alterar a extensão do certificado de raiz para. crt e confirme. O ícone de ficheiro é alterado para um que representa um certificado de raiz.

7. Faça duplo clique o certificado de raiz e a partir do menu de pop-up, selecione **instalar certificado**.

8. Na **Assistente para importar certificados**, selecione **máquina Local** como o destino para o certificado e, em seguida, clique **seguinte**. Confirme se lhe forem pedidas se pretender permitir alterações para o computador.

    ![Boas-vindas do Assistente](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)


9. Sobre o **Store de certificado** página, selecione **colocar todos os certificados no seguinte arquivo**e, em seguida, clique em **procurar** para escolher o arquivo de certificados.

    ![Armazenamento de certificado](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. Na **selecione certificado Store**, selecione **autoridades de certificação de raiz fidedigna** como a pasta de destino para os certificados e, em seguida, clique **OK**.

    ![Pasta de destino de certificado](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. Na **a concluir o Assistente de importação de certificado**, verifique a pasta e, em seguida, clique em **concluir**.

    ![Verifique se o certificado está na pasta apropriada](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)


12. Após a importação de certificado é confirmar, inicie sessão no vCenter Server para confirmar se a ligação está segura.




### <a name="disable-default-https"></a>Desativar padrão HTTPS

Se o ter limites seguros na sua organização e não pretender utilizar o protocolo HTTPS entre servidores de VMware e a máquina do servidor de cópia de segurança do Azure, desativar o HTTPS da seguinte forma: u
1. Copie e cole o seguinte texto num ficheiro. txt.

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. Guarde o ficheiro no computador de servidor de cópia de segurança do Azure com o nome **DisableSecureAuthentication.reg**.

3. Faça duplo clique no ficheiro para ativar a entrada de registo.


## <a name="create-a-vmware-role"></a>Criar uma função de VMware

O servidor de cópia de segurança do Azure tem uma conta de utilizador com permissões para aceder ao Vcenter Server/anfitrião ESXi. Criar uma função de VMware com privilégios específicos e, em seguida, associar uma conta de utilizador com a função.

1. Inicie sessão para o vCenter Server (ou o anfitrião do ESXi se não estiver a utilizar o vCenter Server).
2. Na **Navigator** painel, clique em **administração**.

    ![Administração ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Na **Administration** > **funções**, clique no ícone de função de adicionar (o símbolo +).

    ![Adicionar função](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)


4. Na **criar função** > **nome da função**, introduza *BackupAdminRole*. O nome da função pode ser que quiser, mas deve ser reconhecível para finalidade da função.

5. Selecione os privilégios, conforme resumido na tabela abaixo e, em seguida, clique em **OK**.  A nova função aparece na lista dos **funções** painel.
    - Clique no ícone junto à etiqueta principal para expandir o pai e ver os privilégios de subordinados.
    - Para selecionar os privilégios de VirtualMachine, terá de ir a vários níveis na hierarquia principal subordinado.
    - Não precisa de selecionar todos os privilégios de filho dentro de um privilégio principal.

    ![Hierarquia de privilégio do principal subordinado](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Permissões de funções
**6.5/6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField |
Host.Local.CreateVM | Network.Assign
Network.Assign |
Resource.AssignVMToPool |
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking
VirtualMachine.Config.HostUSBDevice |
VirtualMachine.Config.QueryUnownedFiles |
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create
VirtualMachine.Provisioning.DiskRandomAccess |
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot




## <a name="create-a-vmware-account"></a>Criar uma conta de VMware

1. No vCenter Server **Navigator** painel, clique em **utilizadores e grupos**. Se não utilizar o vCenter Server, crie a conta no anfitrião ESXi do apropriado.

    ![Opção de utilizadores e grupos](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    O **vCenter utilizadores e grupos** painel é apresentada.


2. Na **vCenter utilizadores e grupos** painel, selecione a **utilizadores** separador e, em seguida, clique no ícone de utilizadores de adicionar (o símbolo +).

     ![Painel de utilizadores e grupos do vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. Na **novo utilizador** caixa de diálogo caixa, adicione as informações de utilizador > **OK**. Neste procedimento, o nome de utilizador é BackupAdmin.

    ![Caixa de diálogo do novo utilizador](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. Para associar a conta de utilizador com a função, o **Navigator** painel, clique em **permissões globais**. Na **permissões globais** painel, selecione a **gerir** separador e, em seguida, clique no ícone de adicionar (o símbolo +).

    ![Painel de permissões global](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. Na **Global de permissão de raiz - adicionar permissão**, clique em **Add** para escolher o utilizador ou grupo.

    ![Escolha o utilizador ou grupo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Na **selecionar os utilizadores/grupos**, escolha **BackupAdmin** > **adicionar**. Na **usuários**, o *domínio \ nomedeutilizador* formato é usado para a conta de utilizador. Se pretender utilizar um domínio diferente, selecione-o a partir da **domínio** lista. Clique em **OK** para adicionar os utilizadores selecionados para o **adicionar permissão** caixa de diálogo.

    ![Adicionar utilizador BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  Na **função atribuída**, na lista pendente, selecione **BackupAdminRole** > **OK**.

    ![Atribuir utilizador à função](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


Sobre o **gerir** separador a **permissões globais** painel, a nova conta de utilizador e a função associada são apresentados na lista.


## <a name="add-the-account-on-azure-backup-server"></a>Adicione a conta no servidor de cópia de segurança do Azure


1. Abra o servidor de cópia de segurança do Azure. Se não encontrar o ícone na área de trabalho, abra o Microsoft Azure Backup da lista de aplicações.

    ![Ícone de servidor de cópia de segurança do Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Na consola do servidor de cópia de segurança do Azure, clique em **gerenciamento** >  **servidores de produção** > **gerir VMware**.

    ![Consola do Azure Backup Server](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. Na **gerir credenciais** caixa de diálogo, clique em **Add**.

    ![Caixa de diálogo de credenciais de gerir o servidor de cópia de segurança do Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Na **adicionar credencial** , introduza um nome e uma descrição para a nova credencial e especifique o nome de utilizador e palavra-passe que definiu no servidor do VMware. O nome, *credenciais do Contoso Vcenter* é utilizado para identificar a credencial neste procedimento. Se o servidor VMware e o servidor de cópia de segurança do Azure não estão no mesmo domínio, especifique o domínio no nome do utilizador.

    ![Caixa de diálogo Adicionar credencial do servidor de cópia de segurança do Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Clique em **adicionar** para adicionar a nova credencial.

    ![Caixa de diálogo de credenciais de gerir o servidor de cópia de segurança do Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>Adicionar o vCenter Server

Adicione o vCenter Server para o servidor de cópia de segurança do Azure.


1. Na consola do servidor de cópia de segurança do Azure, clique em **gerenciamento** > **servidores de produção** > **adicionar**.

    ![Assistente de adição do servidor de produção aberto](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)


2. Na **Assistente de adição do servidor de produção** > **tipo selecionar servidor de produção** , selecione **servidores VMware**e, em seguida, clique em **próxima**.

     ![Assistente de adição do servidor de produção](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Na **selecionar computadores****servidor de nome/endereço IP**, especifique o endereço IP ou FQDN do servidor VMware. Se todos os servidores do ESXi são geridos pelo mesmo vCenter, especifique o nome do vCenter. Caso contrário, adicione o anfitrião ESXi.

    ![Especifique o servidor VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Na **porta SSL**, introduza a porta que é utilizada para comunicar com o servidor VMware. 443 é a porta predefinida, mas pode alterá-lo, se o servidor VMware escuta numa porta diferente.

5. Na **especificar credenciais**, selecione a credencial que criou anteriormente.

    ![Especificar credenciais](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Clique em **adicionar** para adicionar o servidor de VMware para a lista de servidores. Clique depois em **Seguinte**.

    ![Adicionar servidor VMWare e as credenciais](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na **resumo** página, clique em **Add** para adicionar o servidor de VMware para o servidor de cópia de segurança do Azure. O novo servidor é adicionado imediatamente, que sem agente é necessária no servidor do VMware.

    ![Adicionar servidor VMware para o servidor de cópia de segurança do Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Verifique as definições no **concluir** página.

  ![Página de conclusão](./media/backup-azure-backup-server-vmware/summary-screen.png)

Se tiver vários anfitriões ESXi que não são geridos pelo vCenter server, ou se tiver várias instâncias do vCenter Server, terá de executar novamente o Assistente para adicionar os servidores.




## <a name="configure-a-protection-group"></a>Configurar um grupo de proteção

Adicione VMs de VMware para cópia de segurança. Grupos de proteção reunir várias VMs e aplicam as definições de cópia de segurança e retenção de dados mesmo a todas as VMs no grupo.


1. Na consola do servidor de cópia de segurança do Azure, clique em **proteção**, > **New**.

    ![Abrir o Assistente Criar novo grupo de proteção](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

2. Na **criar novo grupo de proteção** página de boas-vindas do assistente, clique em **próxima**.

    ![Caixa de diálogo do Assistente Criar novo grupo de proteção](./media/backup-azure-backup-server-vmware/protection-wizard.png)

3. Sobre o **tipo de grupo de proteção de selecionar** , selecione **servidores** e, em seguida, clique em **seguinte**. O **selecionar membros do grupo** é apresentada a página.

3. Na **selecionar membros do grupo** > Selecione as VMs (ou de pastas VM) que pretende criar cópias de segurança. Clique depois em **Seguinte**.

    - Quando seleciona uma pasta ou VMs ou pastas dentro dessa pasta também são selecionadas para cópia de segurança. Pode desmarcar pastas ou VMs que não pretende criar cópias de segurança.
- Se uma VM ou pasta está já a cópia de segurança, não é possível selecioná-lo. Isso certifique-se de que não são criados pontos de recuperação duplicadas para uma VM. .

    ![Selecionar Membros do grupo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


4. Na **selecionar método de proteção de dados** página, introduza um nome para o grupo de proteção e definições de proteção. De volta para o Azure, definir proteção a curto prazo para **disco** e ativar a proteção online. Clique depois em **Seguinte**.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Na **especificar objetivos a curto prazo**, especifique o período de tempo que pretende manter os dados de cópia de segurança para disco.
    - Na **período de retenção**, especifique o número de dias pontos de recuperação do disco devem ser mantidos.
    - Na **frequência de sincronização**, especifique a frequência com que os pontos de recuperação do disco são obtidos.
        - Se não quiser definir uma cópia de segurança intervalo pode verificar **apenas antes de um ponto de recuperação** para que seja executada uma cópia de segurança antes de cada ponto de recuperação ser agendado.
        - As cópias de segurança de curta duração são cópias de segurança completas e não incremental.
        - Clique em **modificar** para alterar o vezes/datas quando ocorrerem cópias de segurança de curta duração.

    ![Especificar objetivos de curto prazo](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Na **rever atribuição do disco**, reveja o espaço em disco fornecido para as cópias de segurança VM. para as VMs.

    - As alocações de disco recomendado baseiam-se o intervalo de retenção especificado, o tipo de carga de trabalho e o tamanho dos dados protegidos. Efetue as alterações necessárias e, em seguida, clique em **seguinte**.
    -  **Tamanho de dados:** Tamanho dos dados no grupo de proteção.
    - **Espaço em disco:** A quantidade de espaço em disco para o grupo de proteção recomendada. Se pretender modificar esta definição, deve alocar espaço total ligeiramente maior do que a quantidade que estimar o que aumenta a cada origem de dados.
    - **Colocalizar dados:** Se ativar a partilha de localização, podem mapear várias origens de dados a proteção para uma única réplica e volume de pontos de recuperação. A colocalização não é suportada para todas as cargas de trabalho.
    - **Aumente automaticamente:** Se ativar esta definição, caso os dados no grupo de proteção superem a alocação inicial, o servidor de cópia de segurança do Azure tenta aumentar o tamanho de disco em 25 por cento.
    - **Detalhes do agrupamento de armazenamento:** Mostra o estado do agrupamento de armazenamento, incluindo total e restante o tamanho do disco.

    ![Rever atribuição do disco](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

7. Na **Choose Replica Creation Method** , especifique como pretende efetuar a cópia de segurança inicial e, em seguida, clique em **próxima**.
    - A predefinição é **automaticamente através da rede** e **agora**.
    - Se utilizar a predefinição, é recomendável que especificar um horário fora de pico. Escolher **mais tarde** e especifique um dia e hora.
    - Para grandes quantidades de dados ou condições de rede aquém do ideal, considere replicar os dados offline usando mídia removível.

    ![Escolher método de criação de réplica](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Na **opções de verificação de consistência**, selecione como e quando automatizar as verificações de consistência. Clique depois em **Seguinte**.
    - Pode executar verificações de consistência quando os dados de réplica se tornar inconsistentes ou numa agenda definida.
    - Se não quiser configurar verificações de consistência automáticas, pode executar uma verificação manual. Para tal, clique com botão direito do grupo de proteção > **efetuar verificação de consistência**.

9. Na **especificar dados da proteção Online** , selecione a VM de VMs ou pastas que pretende criar cópias de segurança. Pode selecionar os membros individualmente, ou clique em **Selecionar tudo** escolher todos os membros. Clique depois em **Seguinte**.

    ![Especificar dados da proteção online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Sobre o **Especificar agenda de cópia de segurança Online** , especifique a frequência com que pretende fazer uma cópia de segurança de dados do armazenamento local para o Azure.

    - Pontos de recuperação na cloud para os dados serão gerados, de acordo com a agenda. Clique depois em **Seguinte**.
    - Depois do ponto de recuperação é gerado, é transferido para o Cofre de serviços de recuperação no Azure.

    ![Especifique a agenda de cópia de segurança online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Sobre o **especificar política de retenção Online** página, indique o período de tempo pretende manter os pontos de recuperação criados a partir de cópias de segurança diárias/semanais/mensais/anuais para o Azure. Em seguida, clique em **seguinte**.

    - Não existe nenhum limite de tempo para o tempo que pode manter os dados no Azure.
    - O único limite é que não pode ter mais de 9999 pontos de recuperação por instância protegida. Neste exemplo, a instância protegida é o servidor VMware.

    ![Especificar Política de retenção online](./media/backup-azure-backup-server-vmware/retention-policy.png)


12. Sobre o **resumo** página, reveja as definições e, em seguida, clique em **criar grupo**.

    ![Membro do grupo de proteção e o resumo de definição](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

A tarefa de cópia de segurança vSphere 6.7 o seguinte:

- Ativar o TLS 1.2 no servidor DPM
  >[!Note]
  >6.7 de VMWare e posteriores tiver habilitado a TLS como protocolo de comunicação.

- Defina as chaves de registo da seguinte forma:  

  Editor de registo do Windows versão 5.00

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 s"SchUseStrongCrypto"=dword:00000001


## <a name="next-steps"></a>Passos Seguintes

Para resolução de problemas ao configurar cópias de segurança, reveja os [solução de problemas para o Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
