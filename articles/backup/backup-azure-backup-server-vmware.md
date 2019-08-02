---
title: Fazer backup de VMs VMware com Servidor de Backup do Azure
description: Use Servidor de Backup do Azure para fazer backup de VMs VMware em execução em um servidor VMware vCenter/ESXi.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: dacurwin
ms.openlocfilehash: c53e2c383739b717a5ce94c872b4616bbd1b3f26
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639948"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Fazer backup de VMs VMware com Servidor de Backup do Azure

Este artigo explica como fazer backup de VMs VMware em execução em hosts VMware ESXi/vCenter Server no Azure usando Servidor de Backup do Azure.

Este artigo explica como:

- Configure um canal seguro para que Servidor de Backup do Azure possa se comunicar com servidores VMware por HTTPS.
- Configure uma conta do VMware que Servidor de Backup do Azure usa para acessar o servidor VMware.
- Adicione as credenciais da conta ao backup do Azure.
- Adicione o servidor vCenter ou ESXi a Servidor de Backup do Azure.
- Configure um grupo de proteção que contenha as VMs do VMware que você deseja fazer backup, especifique as configurações de backup e agende o backup.

## <a name="before-you-start"></a>Antes de começar
- Verifique se você está executando uma versão do vCenter/ESXi com suporte para as versões 6,5, 6,0 e 5,5 do backup.
- Verifique se você configurou Servidor de Backup do Azure. Se você ainda não fez [isso, faça isso](backup-azure-microsoft-azure-backup.md) antes de começar. Você deve estar executando Servidor de Backup do Azure com as atualizações mais recentes.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma conexão segura com o vCenter Server

Por padrão, o Servidor de Backup do Azure se comunica com servidores VMware por HTTPS. Para configurar a conexão HTTPS, baixe o certificado da AC (autoridade de certificação) do VMware e importe-o no Servidor de Backup do Azure.


### <a name="before-you-start"></a>Antes de começar

- Se você não quiser usar HTTPS, poderá [desabilitar a validação de certificado HTTPS para todos os servidores VMware](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Normalmente, você se conecta de um navegador na máquina Servidor de Backup do Azure ao servidor vCenter/ESXi usando o cliente Web vSphere. Na primeira vez que você fizer isso, a conexão não será segura e mostrará o seguinte.
- É importante entender como Servidor de Backup do Azure lida com os backups.
    - Como uma primeira etapa Servidor de Backup do Azure faz backup dos dados no armazenamento em disco local. Servidor de Backup do Azure usa um pool de armazenamento, um conjunto de discos e volumes nos quais o Servidor de Backup do Azure armazena pontos de recuperação de disco para seus dados protegidos. O pool de armazenamento pode ser DAS (armazenamento conectado diretamente), um SAN de canal de fibra ou um dispositivo de armazenamento iSCSI ou SAN. É importante garantir que você tenha armazenamento suficiente para backup local de seus dados de VM do VMware.
    - Em seguida, Servidor de Backup do Azure faz backup do armazenamento em disco local para o Azure.
    - [Obtenha ajuda](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) para descobrir quanto espaço de armazenamento você precisa. As informações são para o DPM, mas também podem ser usadas para Servidor de Backup do Azure.

### <a name="set-up-the-certificate"></a>Configurar o certificado

Configure um canal seguro da seguinte maneira:

1. No navegador em Servidor de Backup do Azure, insira a URL do cliente Web vSphere. Se a página de logon não for exibida, verifique as configurações de conexão e proxy do navegador.

    ![Cliente Web vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na página de logon do cliente Web vSphere, clique em **baixar certificados de AC raiz confiável**.

    ![Baixar certificado de AC raiz confiável](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Um arquivo chamado **Download** é baixado. Dependendo do seu navegador, você receberá uma mensagem que pergunta se deseja abrir ou salvar o arquivo.

    ![Baixar certificado de autoridade de certificação](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Salve o arquivo no computador Servidor de Backup do Azure com uma extensão. zip.

5. Clique com o botão direito do mouse em **baixar. zip** > **extrair tudo**. O arquivo. zip extrai seu conteúdo para a pasta **certs** , que contém:
   - O arquivo de certificado raiz com uma extensão que começa com uma sequência numerada como. 0 e. 1.
   - O arquivo de CRL tem uma extensão que começa com uma sequência como. R0 ou. R1. O arquivo de CRL está associado a um certificado.

     ![Certificados baixados](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Na pasta **certificados** , clique com o botão direito do mouse no arquivo docertificado raiz > Renomear.

    ![Renomear certificado raiz](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. Altere a extensão do certificado raiz para. CRT e confirme. O ícone de arquivo é alterado para um que representa um certificado raiz.

7. Clique com o botão direito do mouse no certificado raiz e, no menu pop-up, selecione **Instalar certificado**.

8. No **Assistente para importação de certificados**, selecione **computador local** como o destino do certificado e clique em **Avançar**. Confirme se você é perguntado se deseja permitir alterações no computador.

    ![Assistente de boas-vindas](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)


9. Na página **repositório de certificados** , selecione **Coloque todos os certificados no repositório a seguir**e clique em **procurar** para escolher o repositório de certificados.

     ![Armazenamento de certificado](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. Em **selecionar repositório de certificados**, selecione **autoridades de certificação raiz confiáveis** como a pasta de destino para os certificados e clique em **OK**.

    ![Pasta de destino do certificado](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. Em **concluindo o assistente para importação de certificados**, verifique a pasta e clique em **concluir**.

    ![Verifique se o certificado está na pasta apropriada](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)


12. Depois que a importação do certificado for confirmada, entre no vCenter Server para confirmar que sua conexão é segura.




### <a name="disable-https-certificate-validation"></a>Desabilitar validação de certificado HTTPS

Se você tiver limites de segurança em sua organização e não quiser usar o protocolo HTTPS entre servidores VMware e o computador Servidor de Backup do Azure, desabilite o HTTPS da seguinte maneira: u
1. Copie e cole o texto a seguir em um arquivo. txt.

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. Salve o arquivo no computador Servidor de Backup do Azure com o nome **DisableSecureAuthentication. reg**.

3. Clique duas vezes no arquivo para ativar a entrada do registro.


## <a name="create-a-vmware-role"></a>Criar uma função VMware

O Servidor de Backup do Azure precisa de uma conta de usuário com permissões para acessar o servidor do v-Center/host ESXi. Crie uma função VMware com privilégios específicos e associe uma conta de usuário à função.

1. Entre no vCenter Server (ou host ESXi se você não estiver usando vCenter Server).
2. No painel **navegador** , clique em **Administração**.

    ![Administração](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Em**funções**de **Administração** > , clique no ícone Adicionar função (o símbolo +).

    ![Adicionar função](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)


4. Em **criar** > **nome da função**de função, insira *BackupAdminRole*. O nome da função pode ser o que você desejar, mas deve ser reconhecível para a finalidade da função.

5. Selecione os privilégios conforme resumido na tabela a seguir e clique em **OK**.  A nova função é exibida na lista no painel **funções** .
   - Clique no ícone ao lado do rótulo pai para expandir o pai e exibir os privilégios filho.
   - Para selecionar os privilégios de VirtualMachine, você precisa ir vários níveis para a hierarquia pai filho.
   - Você não precisa selecionar todos os privilégios filho dentro de um privilégio pai.

     ![Hierarquia de privilégios pai filho](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Permissões de funções
**6.5/6.0** | **5,5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField |
Host.Local.CreateVM | Rede. atribuir
Rede. atribuir |
Resource. AssignVMToPool |
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking
VirtualMachine.Config.HostUSBDevice |
VirtualMachine.Config.QueryUnownedFiles |
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff
VirtualMachine. Inventory. Create| VirtualMachine. Inventory. Create
VirtualMachine.Provisioning.DiskRandomAccess |
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot




## <a name="create-a-vmware-account"></a>Criar uma conta do VMware

1. No painel vCenter Server **navegador** , clique em **usuários e grupos**. Se você não usar vCenter Server, crie a conta no host ESXi apropriado.

    ![Opção de usuários e grupos](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    O painel **usuários e grupos do vCenter** é exibido.


2. No painel **usuários e grupos do vCenter** , selecione a guia **usuários** e, em seguida, clique no ícone Adicionar usuários (o símbolo +).

     ![painel usuários e grupos do vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. Na caixa de diálogo **novo usuário** , adicione as informações do usuário > **OK**. Neste procedimento, o nome de usuário é BackupAdmin.

    ![Caixa de diálogo novo usuário](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. Para associar a conta de usuário à função, no painel **navegador** , clique em **permissões globais**. No painel **permissões globais** , selecione a guia **gerenciar** e, em seguida, clique no ícone Adicionar (o símbolo +).

    ![Painel de permissões globais](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. Na permissão **global-adicionar permissão de raiz**, clique em **Adicionar** para escolher o usuário ou grupo.

    ![Escolher usuário ou grupo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Em **Selecionar usuários/grupos**, escolha **BackupAdmin** > **Adicionar**. Em **usuários**, o formato *domínio \ nomedeusuário* é usado para a conta de usuário. Se você quiser usar um domínio diferente, escolha-o na lista de **domínios** . Clique em **OK** para adicionar os usuários selecionados à caixa de diálogo **adicionar permissão** .

    ![Adicionar usuário BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  Em **função atribuída**, na lista suspensa, selecione **BackupAdminRole** > **OK**.

    ![Atribuir usuário à função](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


Na guia **gerenciar** do painel **permissões globais** , a nova conta de usuário e a função associada aparecem na lista.


## <a name="add-the-account-on-azure-backup-server"></a>Adicionar a conta no Servidor de Backup do Azure


1. Abra Servidor de Backup do Azure. Se você não encontrar o ícone na área de trabalho, abra Backup do Microsoft Azure na lista de aplicativos.

    ![Ícone de Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. No console do servidor de backup do Azure, clique em **Gerenciamento** >  **servidores** > de produção**gerenciar VMware**.

    ![Console do Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. Na caixa de diálogo **gerenciar credenciais** , clique em **Adicionar**.

    ![Caixa de diálogo Servidor de Backup do Azure gerenciar credenciais](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Em **Adicionar credencial** , insira um nome e uma descrição para a nova credencial e especifique o nome de usuário e a senha que você definiu no servidor VMware. O nome, *credencial do contoso vCenter* , é usado para identificar a credencial neste procedimento. Se o servidor VMware e Servidor de Backup do Azure não estiverem no mesmo domínio, especifique o domínio no nome de usuário.

    ![Caixa de diálogo Servidor de Backup do Azure adicionar credencial](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Clique em **Adicionar** para adicionar a nova credencial.

    ![Caixa de diálogo Servidor de Backup do Azure gerenciar credenciais](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>Adicionar o vCenter Server

Adicione o vCenter Server ao Servidor de Backup do Azure.


1. No console do servidor de backup do Azure, clique em **Gerenciamento** > **servidores** > de produção**Adicionar**.

    ![Abrir assistente de adição de servidor de produção](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)


2. No **Assistente** > de adição de servidor de produção, selecione a página**tipo de servidor de produção** , selecione **servidores VMware**e clique em **Avançar**.

     ![Assistente de adição de servidor de produção](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Em **Selecionar computadores**  **nome do servidor/endereço IP**, especifique o FQDN ou o endereço IP do servidor VMware. Se todos os servidores ESXi forem gerenciados pelo mesmo vCenter, especifique o nome do vCenter. Caso contrário, adicione o host ESXi.

    ![Especificar servidor VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Em **porta SSL**, insira a porta que é usada para se comunicar com o servidor VMware. 443 é a porta padrão, mas você pode alterá-la se o servidor VMware escutar em uma porta diferente.

5. Em **especificar credencial**, selecione a credencial que você criou anteriormente.

    ![Especificar credencial](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Clique em **Adicionar** para adicionar o servidor VMware à lista servidores. Clique depois em **Seguinte**.

    ![Adicionar servidor VMWare e credencial](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na página **Resumo** , clique em **Adicionar** para adicionar o servidor VMware ao servidor de backup do Azure. O novo servidor é adicionado imediatamente, nenhum agente é necessário no servidor VMware.

    ![Adicionar servidor VMware ao Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Verifique as configurações na página **concluir** .

   ![Página concluir](./media/backup-azure-backup-server-vmware/summary-screen.png)

Se você tiver vários hosts ESXi que não são gerenciados pelo vCenter Server ou tiver várias instâncias do vCenter Server, será necessário executar novamente o assistente para adicionar os servidores.




## <a name="configure-a-protection-group"></a>Configurar um grupo de proteção

Adicione VMs VMware para backup. Os grupos de proteção reúnem várias VMs e aplicam as mesmas configurações de retenção de dados e backup a todas as VMs no grupo.


1. No console do Servidor de Backup do Azure, clique em **proteção**> **novo**.

    ![Abrir o assistente para criar novo grupo de proteção](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na página de boas-vindas do assistente para **criar novo grupo de proteção** , clique em **Avançar**.

    ![Caixa de diálogo Assistente para criar novo grupo de proteção](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na página **Selecionar tipo de grupo de proteção** , selecione **servidores** e clique em **Avançar**. A página **selecionar membros do grupo** é exibida.

1. Em **selecionar membros do grupo** > selecione as VMs (ou as pastas de VM) que você deseja fazer backup. Clique depois em **Seguinte**.

    - Quando você seleciona uma pasta, ou as VMs ou pastas dentro dessa pasta também são selecionadas para backup. Você pode desmarcar as pastas ou VMs das quais você não deseja fazer backup.
1. Se uma VM ou pasta já estiver sendo submetida a backup, você não poderá selecioná-la. Isso garante que os pontos de recuperação duplicados não sejam criados para uma VM. .

     ![Selecionar membros do grupo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


1. Na página **selecionar método de proteção de dados** , insira um nome para o grupo de proteção e as configurações de proteção. Para fazer backup no Azure, defina a proteção de curto prazo em **disco** e habilite a proteção online. Clique depois em **Seguinte**.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Em **especificar objetivos de curto prazo**, especifique por quanto tempo deseja manter os dados de backup em disco.
   - Em **período de retenção**, especifique quantos dias os pontos de recuperação de disco devem ser mantidos.
   - Em **frequência de sincronização**, especifique com que frequência os pontos de recuperação de disco são obtidos.
       - Se você não quiser definir um intervalo de backup, poderá verificar **logo antes de um ponto de recuperação** para que um backup seja executado logo antes de cada ponto de recuperação ser agendado.
       - Backups de curto prazo são backups completos e não incrementais.
       - Clique em **Modificar** para alterar os horários/datas em que ocorrem os backups de curto prazo.

     ![Especificar objetivos de curto prazo](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Em **examinar alocação de disco**, examine o espaço em disco fornecido para os backups de VM. para as VMs.

   - As alocações de disco recomendadas são baseadas no período de retenção especificado, no tipo de carga de trabalho e no tamanho dos dados protegidos. Faça as alterações necessárias e clique em **Avançar**.
   - **Tamanho dos dados:** Tamanho dos dados no grupo de proteção.
   - **Espaço em disco:** A quantidade recomendada de espaço em disco para o grupo de proteção. Se você quiser modificar essa configuração, deverá alocar um espaço total que seja ligeiramente maior do que o valor estimado de cada fonte de dados.
   - **Colocar dados:** Se você ativar a colocação, várias fontes de dados na proteção poderão ser mapeadas para uma única réplica e volume de ponto de recuperação. A colocação não tem suporte para todas as cargas de trabalho.
   - **Aumentar automaticamente:** Se você ativar essa configuração, se os dados no grupo protegido ultrapassarem a alocação inicial, Servidor de Backup do Azure tentará aumentar o tamanho do disco em 25%.
   - **Detalhes do pool de armazenamento:** Mostra o status do pool de armazenamento, incluindo o tamanho do disco total e restante.

     ![Examinar alocação do disco](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Na página **escolher método de criação de réplica** , especifique como deseja fazer o backup inicial e clique em **Avançar**.
   - O padrão é **automaticamente pela rede** e **agora**.
   - Se você usar o padrão, recomendamos que você especifique um horário de pico. Escolha **mais tarde** e especifique um dia e hora.
   - Para grandes quantidades de dados ou condições de rede menos do ideal, considere a possibilidade de replicar os dados offline usando mídia removível.

     ![Escolher método de criação de réplica](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Em **Opções de verificação de consistência**, selecione como e quando automatizar as verificações de consistência. Clique depois em **Seguinte**.
      - Você pode executar verificações de consistência quando os dados da réplica se tornarem inconsistentes ou em uma agenda definida.
      - Se você não quiser configurar verificações de consistência automáticas, poderá executar uma verificação manual. Para fazer isso, clique com o botão direito do mouse no grupo de proteção > **executar a verificação de consistência**.

1. Na página **especificar dados de proteção online** , selecione as VMs ou as pastas de VM que você deseja fazer backup. Você pode selecionar os membros individualmente ou clicar em **selecionar tudo** para escolher todos os membros. Clique depois em **Seguinte**.

      ![Especificar dados de proteção online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na página **especificar agendamento de backup online** , especifique com que frequência você deseja fazer backup de dados do armazenamento local para o Azure.

    - Os pontos de recuperação de nuvem para os dados serão gerados de acordo com o agendamento. Clique depois em **Seguinte**.
    - Depois que o ponto de recuperação é gerado, ele é transferido para o cofre dos serviços de recuperação no Azure.

      ![Especificar agendamento de backup online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na página **especificar política de retenção online** , indique por quanto tempo deseja manter os pontos de recuperação criados a partir dos backups diários/semanais/mensais/anuais para o Azure. em seguida, clique em **Avançar**.

    - Não há limite de tempo por quanto tempo você pode manter os dados no Azure.
    - O único limite é que você não pode ter mais de 9999 pontos de recuperação por instância protegida. Neste exemplo, a instância protegida é o servidor VMware.

      ![Especificar política de retenção online](./media/backup-azure-backup-server-vmware/retention-policy.png)


1. Na página **Resumo** , examine as configurações e clique em **Criar grupo**.

     ![Resumo de configuração e membro do grupo de proteção](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

Para fazer backup do vSphere 6,7, faça o seguinte:

- Habilitar o TLS 1,2 no servidor DPM
  >[!Note]
  >O VMWare 6,7 em diante habilitou o TLS como protocolo de comunicação.

- Defina as chaves do registro da seguinte maneira:  

  Editor do registro do Windows versão 5, 0

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 s"SchUseStrongCrypto"=dword:00000001


## <a name="next-steps"></a>Passos Seguintes

Para solucionar problemas ao configurar backups, examine o [Guia de solução de problemas para servidor de backup do Azure](./backup-azure-mabs-troubleshoot.md).
