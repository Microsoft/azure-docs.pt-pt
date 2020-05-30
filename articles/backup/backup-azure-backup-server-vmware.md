---
title: Backup VMware VMs com Azure Backup Server
description: Neste artigo, aprenda a utilizar o Azure Backup Server para fazer backup vMware VMs em execução num servidor VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: c4bf61e2a02200b2e6af814ef4509081649e202d
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204723"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Backup VMware VMs com Azure Backup Server

Este artigo explica como fazer backup vMware VMware VMs em execução em VMware ESXi hosts/vCenter Server para Azure usando O Servidor de Backup Azure.

Este artigo explica como:

- Crie um canal seguro para que o Azure Backup Server possa comunicar com servidores VMware através de HTTPS.
- Crie uma conta VMware que o Azure Backup Server utiliza para aceder ao servidor VMware.
- Adicione as credenciais de conta ao Azure Backup.
- Adicione o servidor vCenter ou ESXi ao Servidor de Backup Azure.
- Crie um grupo de proteção que contenha os VMware VMs que pretende fazer cópias de segurança, especifique as definições de backup e agende a cópia de segurança.

## <a name="before-you-start"></a>Antes de começar

- Verifique se está a executar uma versão do vCenter/ESXi que é suportada por cópias de segurança. Consulte aqui a [matriz](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix)de suporte.
- Certifique-se de que configura o Servidor de Backup Azure. Se não o fez, [faça isso](backup-azure-microsoft-azure-backup.md) antes de começar. Deverá estar a executar o Azure Backup Server com as últimas atualizações.
- Certifique-se de que as seguintes portas de rede estão abertas:
    - TCP 443 entre MABS e vCenter
    - TCP 443 e TCP 902 entre mABS e anfitrião ESXi


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma ligação segura ao servidor vCenter

Por predefinição, o Azure Backup Server comunica com servidores VMware através de HTTPS. Para configurar a ligação HTTPS, descarregue o certificado VMware Certificate Authority (CA) e importe-o no Servidor de Backup Azure.

### <a name="before-you-begin"></a>Antes de começar

- Se não quiser utilizar HTTPS, pode [desativar a validação do certificado HTTPS para todos os servidores VMware](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Normalmente, liga-se de um navegador na máquina Azure Backup Server ao servidor vCenter/ESXi utilizando o cliente web vSphere. A primeira vez que fizer isto, a ligação não é segura e mostrará o seguinte.
- É importante entender como o Azure Backup Server lida com cópias de segurança.
  - Como primeiro passo, o Azure Backup Server regista dados para o armazenamento de discos locais. O Azure Backup Server utiliza uma piscina de armazenamento, um conjunto de discos e volumes nos quais o Azure Backup Server armazena pontos de recuperação de discos para os seus dados protegidos. A piscina de armazenamento pode ser anexada diretamente ao armazenamento (DAS), um canal de fibra SAN, ou dispositivo de armazenamento iSCSI ou SAN. É importante garantir que tem armazenamento suficiente para a cópia de segurança local dos seus dados VMware VMware VM.
  - O Azure Backup Server, em seguida, recua do armazenamento do disco local para o Azure.
  - [Obtenha ajuda](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) para descobrir quanto espaço de armazenamento precisa. A informação é para DPM mas também pode ser usada para O Servidor de Backup Azure.

### <a name="set-up-the-certificate"></a>Configurar o certificado

Crie um canal seguro da seguinte forma:

1. No navegador do Azure Backup Server, insira o URL do cliente web vSphere. Se a página de login não aparecer, verifique as definições de procuração de ligação e navegador.

    ![vSphere Web Client](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na página de login do vSphere Web Client, clique em **Baixar certificados de CA de raiz fidedigna**.

    ![Descarregue o certificado ca raiz fidedigno](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Um ficheiro chamado **download** é descarregado. Dependendo do seu navegador, recebe uma mensagem que pergunta se abre ou guarda o ficheiro.

    ![Baixar certificado ca](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Guarde o ficheiro na máquina Azure Backup Server com uma extensão .zip.

5. **Download.zip**  >  **Extract All**. O ficheiro .zip extrai o seu conteúdo para a pasta **certs,** que contém:
   - O ficheiro do certificado raiz com uma extensão que começa com uma sequência numerada como .0 e .1.
   - O ficheiro CRL tem uma extensão que começa com uma sequência como .r0 ou .r1. O ficheiro CRL está associado a um certificado.

    ![Certificados descarregados](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Na pasta **certs,** clique com o botão direito no ficheiro de certificado de raiz > **Renomeado .**

    ![Certificado de raiz de renome](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Altere a extensão do certificado de raiz para .crt, e confirme. O ícone do ficheiro muda para um que representa um certificado de raiz.

8. Clique com o botão direito no certificado raiz e no menu pop-up, **selecione Install Certificate**.

9. No **Certificado Import Wizard**, selecione Máquina **Local** como destino para o certificado e, em seguida, clique em **Seguinte**. Confirme se lhe perguntam se pretende permitir alterações no computador.

    ![Bem-vindo ao Feiticeiro](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Na página **Certificate Store,** selecione **Coloque todos os certificados na seguinte loja**e, em seguida, clique em procurar para escolher a loja de **certificados.**

    ![Armazenamento de certificados](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. Na **Select Certificate Store**, selecione As **Autoridades de Certificação de Raiz Fidedigna como** a pasta de destino para os certificados e, em seguida, clique em **OK**.

    ![Pasta de destino certificado](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. Ao **Completar o Assistente de Importação de Certificados,** verifique a pasta e clique em **Terminar**.

    ![Verifique se o certificado está na pasta adequada](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Após a confirmação da importação do certificado, inscreva-se no servidor vCenter para confirmar que a sua ligação está segura.

### <a name="disable-https-certificate-validation"></a>Desativar validação de certificados HTTPS

Se tiver limites seguros dentro da sua organização e não quiser utilizar o protocolo HTTPS entre servidores VMware e a máquina do Servidor backup Azure, desative o HTTPS da seguinte forma:

1. Copie e cole o texto seguinte num ficheiro .txt.

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. Guarde o ficheiro na máquina do Servidor de Backup Azure com o nome **DisableSecureAuthentication.reg**.

3. Clique duas vezes no ficheiro para ativar a entrada de registo.

## <a name="create-a-vmware-role"></a>Criar um papel VMware

O Azure Backup Server necessita de uma conta de utilizador com permissões para aceder ao servidor/anfitrião V-Center Server/ESXi. Crie uma função VMware com privilégios específicos e, em seguida, associe uma conta de utilizador com o papel.

1. Inscreva-se no servidor vCenter (ou anfitrião ESXi se não estiver a utilizar o servidor vCenter).
2. No painel **Navigator,** clique em **Administração.**

    ![Administração](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Em **Funções de Administração,**  >  **Roles**clique no ícone de função de adicionar (o símbolo +).

    ![Adicionar papel](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. In **Create Role**Role  >  **name**, *insin BackupAdminRole*. O nome pode ser o que quiser, mas deve ser reconhecível para o propósito do papel.

5. Selecione os privilégios como resumido na tabela abaixo e, em seguida, clique em **OK**.  O novo papel aparece na lista no painel **Roles.**
   - Clique no ícone ao lado da etiqueta dos pais para expandir o progenitor e ver os privilégios da criança.
   - Para selecionar os privilégios VirtualMachine, você precisa ir a vários níveis para a hierarquia dos pais filhos.
   - Não precisas de selecionar todos os privilégios infantis dentro de um privilégio dos pais.

    ![Hierarquia do privilégio do filho-mãe](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Permissões de funções

| Privilégios para conta de utilizador vCenter 6.7                     | Privilégios para conta de utilizador vCenter 6.5                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Agrupamento de datas. Configurar um cluster de datatstore            | Agrupamento de datas. Configurar um cluster de datatstore            |
| Datastore.AlocarSpace                                      | Datastore.AlocarSpace                                      |
| Datastore.Navegue na loja de dados                                   | Datastore.Navegue na loja de dados                                   |
| Datastore.Operações de ficheiros de baixo nível                          | Datastore.Operações de ficheiros de baixo nível                          |
| Global.Métodos de desativação                                       | Global.Métodos de desativação                                       |
| Global.Permitir métodos                                        | Global.Permitir métodos                                        |
| Global.Licenças                                              | Global.Licenças                                              |
| Evento Global.Log                                             | Evento Global.Log                                             |
| Global.Gerir atributos personalizados                              | Global.Gerir atributos personalizados                              |
| Global.Definir atributo personalizado                                  | Global.Definir atributo personalizado                                  |
| Host.Operações locais. Criar máquina virtual                | Host.Operações locais. Criar máquina virtual                |
| Rede.Atribuir rede                                       | Rede.Atribuir rede                                       |
| Recurso. Atribuir máquina virtual ao pool de recursos           | Recurso. Atribuir máquina virtual ao pool de recursos           |
| vApp.Adicionar máquina virtual                                     | vApp.Adicionar máquina virtual                                     |
| vApp.Atribua o conjunto de recursos                                    | vApp.Atribua o conjunto de recursos                                    |
| vApp.Unregister                                              | vApp.Unregister                                              |
| VirtualMachine.Configuração. Adicionar ou remover dispositivo          | VirtualMachine.Configuração. Adicionar ou remover dispositivo          |
| Máquina virtual. Configuração.Adquirir arrendamento de discos            | Máquina virtual. Configuração.Aluguer de discos                     |
| Máquina virtual. Configuração.Adicionar novo disco                   | Máquina virtual. Configuração.Adicionar novo disco                   |
| Máquina virtual. Configuração.Configuração avançada        | Máquina virtual. Configuração.Avançado                       |
| Máquina virtual. Configuração.Toggle rastreio de alteração de disco   | Máquina virtual. Rastreio de alteração de configuração.disco          |
| Máquina virtual. Configuração.Configure o dispositivo USB do anfitrião     | Máquina virtual. Configuração.Host USB               |
| Máquina virtual. Configuração.Estender o disco virtual           | Máquina virtual. Configuração.Estender o disco virtual           |
| Máquina virtual. Configuração.Consulta ficheiros não-damedados           | Máquina virtual. Configuração.Consulta ficheiros não-damedados           |
| Máquina virtual. Configuração.Alterar a colocação do ficheiro de swap     | Máquina virtual. Configuração.Troca de ficheiro            |
| Máquina virtual. Execução do Programa de Operações de Hóspedes.Programa de Operação convidado | Máquina virtual. Execução do Programa de Operações de Hóspedes.Programa de Operação convidado |
| Máquina virtual. Operações de Hóspedes.Modificações da Operação de Hóspedes | Máquina virtual. Operações de Hóspedes.Modificações da Operação de Hóspedes |
| Máquina virtual. Operações de Hóspedes.Consultas de Operação de Hóspedes    | Máquina virtual. Operações de Hóspedes.Consultas de Operação de Hóspedes    |
| Máquina virtual. Interação . Ligação do dispositivo             | Máquina virtual. Interação . Ligação do dispositivo             |
| Máquina virtual. Interação . Gestão do sistema operativo convidado pela VIX API | Máquina virtual. Interação . Gestão do sistema operativo convidado pela VIX API |
| Máquina virtual. Interação . Desligar                      | Máquina virtual. Interação . Desligar                      |
| Máquina virtual. Inventário.Criar novo                        | Máquina virtual. Inventário.Criar novo                        |
| Máquina virtual. Inventário.Remover                            | Máquina virtual. Inventário.Remover                            |
| Máquina virtual. Inventário.Registo                          | Máquina virtual. Inventário.Registo                          |
| Máquina virtual. Provisioning.Permitir o acesso ao disco             | Máquina virtual. Provisioning.Permitir o acesso ao disco             |
| Máquina virtual. Provisioning.Permitir o acesso aos ficheiros             | Máquina virtual. Provisioning.Permitir o acesso aos ficheiros             |
| Máquina virtual. Provisioning.Permitir o acesso ao disco apenas de leitura   | Máquina virtual. Provisioning.Permitir o acesso ao disco apenas de leitura   |
| Máquina virtual. Provisioning.Permitir o download de máquinas virtuais | Máquina virtual. Provisioning.Permitir o download de máquinas virtuais |
| Máquina virtual. Gestão de instantâneos.  Criar instantâneo       | Máquina virtual. Gestão de instantâneos.  Criar instantâneo       |
| Máquina virtual. Gestão de instantâneos. Remover instantâneo        | Máquina virtual. Gestão de instantâneos. Remover instantâneo        |
| Máquina virtual. Gestão de instantâneos. Reverter para instantâneo     | Máquina virtual. Gestão de instantâneos. Reverter para instantâneo     |

<br>

| **Privilégios para conta de utilizador vCenter 6.0**                | **Privilégios para conta de utilizador vCenter 5.5** |
| ---------------------------------------------------------- | ------------------------------------------- |
| Datastore.AlocarSpace                                    | Rede.Atribuir                              |
| Global.Gerir atributos personalizados                           | Datastore.AlocarSpace                     |
| Global.Definir atributo personalizado                               | VirtualMachine.Config.ChangeTracking        |
| Host.Operações locais. Criar máquina virtual              | VirtualMachine.State.RemoveSnapshot         |
| A rede.  Atribuir rede                                   | VirtualMachine.State.CreateSnapshot         |
| Recurso.  Atribuir máquina virtual ao pool de recursos         | VirtualMachine.Provisioning.DiskRandomRead  |
| Máquina virtual. Configuração.Adicionar novo disco                | VirtualMachine.Interact.PowerOff            |
| Máquina virtual. Configuração.Avançado                    | VirtualMachine.Inventory.Create             |
| Máquina virtual. Rastreio de alteração de configuração.disco        | VirtualMachine.Config.AddNewDisk            |
| Máquina virtual. Configuração.Host USB             | VirtualMachine.Config.HostUSBDevice         |
| Máquina virtual. Configuração.Consulta ficheiros não-damedados         | VirtualMachine.Config.AdvancedConfig        |
| Máquina virtual. Configuração.Troca de ficheiro          | VirtualMachine.Config.SwapPlacement         |
| Máquina virtual. Interação.Desligar                     | Global.ManageCustomFields                   |
| Máquina virtual. O inventário. Criar novo                     |                                             |
| Máquina virtual. Provisioning.Permitir o acesso ao disco            |                                             |
| Máquina virtual. Provisões. Permitir o acesso ao disco apenas de leitura |                                             |
| Máquina virtual. Gestão de instantâneos. Criar instantâneo       |                                             |
| Máquina virtual. Gestão de instantâneos. Remover instantâneo       |                                             |

## <a name="create-a-vmware-account"></a>Criar uma conta VMware

1. No painel vCenter Server **Navigator,** clique em **Utilizadores e Grupos**. Se não utilizar o vCenter Server, crie a conta no anfitrião ESXi apropriado.

    ![Opção Utilizadores e Grupos](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Aparece o painel **de Utilizadores e Grupos do vCenter.**

2. No painel **vCenter Users and Groups,** selecione o **separador Utilizadores** e, em seguida, clique no ícone de utilizadores de adicionar (o símbolo +).

    ![painel de utilizadores e grupos vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Na caixa de diálogo **do Novo Utilizador,** adicione as informações do utilizador > **OK**. Neste procedimento, o nome de utilizador é BackupAdmin.

    ![Nova caixa de diálogo do utilizador](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Para associar a conta do utilizador à função, no painel **Navigator,** clique em **Permissões Globais**. No painel **Permissões Globais,** selecione o **separador 'Gerir'** e, em seguida, clique no ícone de adicionar (o símbolo +).

    ![Painel de Permissões Globais](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. In **Global Permission Root - Add Permission**, clique em **Adicionar** para escolher o utilizador ou grupo.

    ![Escolha o utilizador ou grupo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Em **Selecionar Utilizadores/Grupos,** escolha **BackupAdmin**  >  **Add**. Nos **Utilizadores,** o formato *de nome de utilizador de domínio\username* é utilizado para a conta do utilizador. Se pretender utilizar um domínio diferente, escolha-o na lista **de Domínios.** Clique **em OK** para adicionar os utilizadores selecionados à caixa de diálogo de permissão de **adicionar.**

    ![Adicionar Backup UtilizadorAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. Em **Papel Atribuído**, a partir da lista de drop-down, selecione **BackupAdminRole**  >  **OK**.

    ![Atribuir o utilizador à função](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

No **separador 'Gerir'** no painel **De permissões Globais,** a nova conta de utilizador e a função associada aparecem na lista.

## <a name="add-the-account-on-azure-backup-server"></a>Adicione a conta no Azure Backup Server

1. Abra o servidor de backup Azure. Se não conseguir encontrar o ícone no ambiente de trabalho, abra o Microsoft Azure Backup da lista de aplicações.

    ![Ícone do servidor de backup Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Na consola Azure Backup **Management**Server, clique em  >   **Servidores de Produção de Gestão**  >  **de Gestão Gerenciar VMware**.

    ![Consola do servidor de backup Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. Na caixa de diálogo **'Gestão de Credenciais',** clique em **Adicionar**.

    ![Azure Backup Servidor Gerencie caixa de diálogo de credenciais](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. In **Add Credential,** insira um nome e uma descrição para a nova credencial, e especifique o nome de utilizador e a palavra-passe que definiu no servidor VMware. O nome, *Credencial Contoso Vcenter* é usado para identificar a credencial neste procedimento. Se o servidor VMware e o Servidor de Backup Azure não estiverem no mesmo domínio, especifique o domínio no nome de utilizador.

    ![Azure Backup Server Adicionar caixa de diálogo credencial](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Clique **em Adicionar** para adicionar a nova credencial.

    ![Azure Backup Servidor Gerencie caixa de diálogo de credenciais](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Adicione o servidor vCenter

Adicione o servidor vCenter ao Servidor de Backup Azure.

1. Na consola Azure Backup **Management**Server, clique em  >  **Servidores de Produção**de Gestão  >  **Add**.

    ![Assistente de adição de servidor de produção aberto](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. No **Assistente de Adição do Servidor de Produção**  >  **Selecione** a página do tipo servidor de produção, selecione **VMware Servers**e, em seguida, clique em **Seguinte**.

    ![Assistente de adição de servidor de produção](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. No Nome do Servidor **de Computadores**  **Select/Endereço IP**, especifique o endereço FQDN ou IP do servidor VMware. Se todos os servidores ESXi forem geridos pelo mesmo vCenter, especifique o nome vCenter. Caso contrário, adicione o anfitrião ESXi.

    ![Especificar servidor VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Na **porta SSL,** entre na porta que é utilizada para comunicar com o servidor VMware. 443 é a porta predefinida, mas pode alterá-la se o seu servidor VMware ouvir numa porta diferente.

5. Em **Especificar Credencial,** selecione a credencial que criou anteriormente.

    ![Especificar credencial](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Clique em **Adicionar** para adicionar o servidor VMware à lista de servidores. Em seguida, clique em **Seguinte**.

    ![Adicionar servidor VMWare e credencial](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na página **Resumo,** clique **em Adicionar** para adicionar o servidor VMware ao Servidor de Backup Azure. O novo servidor é adicionado imediatamente, nenhum agente é necessário no servidor VMware.

    ![Adicione o servidor VMware ao Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Verifique as definições na página **'Acabamento'.**

   ![Página de acabamento](./media/backup-azure-backup-server-vmware/summary-screen.png)

Se tiver vários anfitriões ESXi que não são geridos pelo servidor vCenter, ou se tiver várias instâncias do vCenter Server, tem de refazer o assistente para adicionar os servidores.

## <a name="configure-a-protection-group"></a>Configure um grupo de proteção

Adicione VMware VMs para cópia de segurança. Os grupos de proteção recolhem vários VMs e aplicam as mesmas definições de retenção de dados e backup a todos os VMs do grupo.

1. Na consola Azure Backup Server, clique em **Proteção**, > **New**.

    ![Abra o assistente do Grupo criar novas proteções](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na página de boas-vindas **do grupo de proteção Criar Nova Proteção,** clique em **Seguinte**.

    ![Criar caixa de diálogo de assistente de novo grupo de proteção](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na página do **tipo do grupo Select Protection,** selecione **Servidores** e, em seguida, clique em **Seguinte**. Aparece a página **de membros do grupo Select.**

1. Nos **membros do grupo Select**, selecione as pastas VM (ou VM) que pretende fazer. Em seguida, clique em **Seguinte**.

    - Quando selecionar uma pasta, ou VMs ou pastas no interior dessa pasta também são selecionados para cópia de segurança. Pode desmarcar pastas ou VMs que não quer fazer recuar.
1. Se um VM ou uma pasta já estiver a ser apoiado, não pode selecioná-lo. Isto garante que os pontos de recuperação duplicados não são criados para um VM.

    ![Selecione membros do grupo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na página **Select Data Protection Method,** insira um nome para o grupo de proteção e as definições de proteção. Para apoiar o Azure, desconfiem da proteção de curto prazo ao **Disk** e permitam a proteção online. Em seguida, clique em **Seguinte**.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Em **Especificar Objetivos de Curto Prazo**, especifique quanto tempo pretende manter os dados no disco.
   - No **Intervalo de Retenção**, especifique quantos dias devem ser mantidos pontos de recuperação do disco.
   - Na **frequência de sincronização,** especifique a frequência com que são tomados os pontos de recuperação do disco.
       - Se não quiser definir um intervalo de backup, pode verificar **antes de um ponto de recuperação** para que um backup seja executado antes de cada ponto de recuperação ser programado.
       - As cópias de segurança de curto prazo são cópias de segurança completas e não incrementais.
       - Clique **em Modificar** para alterar as horas/datas quando ocorrem cópias de segurança de curto prazo.

         ![Especificar objetivos de curto prazo](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Na **Atribuição de Discos de Revisão,** reveja o espaço do disco previsto para as cópias de segurança VM. para os VMs.

   - As alocações recomendadas em disco baseiam-se na gama de retenção especificada, no tipo de carga de trabalho e no tamanho dos dados protegidos. Faça as alterações necessárias e, em seguida, clique em **Seguinte**.
   - **Tamanho dos dados:** Tamanho dos dados no grupo de proteção.
   - **Espaço em disco:** A quantidade recomendada de espaço em disco para o grupo de proteção. Se quiser modificar esta definição, deverá alocar um espaço total ligeiramente maior do que a quantidade que estima que cada fonte de dados cresce.
   - **Dados de colocação:** Se ligar a colocação, várias fontes de dados na proteção podem mapear para uma única réplica e volume de ponto de recuperação. A colocação não é suportada para todas as cargas de trabalho.
   - **Crescer automaticamente:** Se ligar esta definição, se os dados do grupo protegido ultrapassarem a alocação inicial, o Azure Backup Server tenta aumentar o tamanho do disco em 25%.
   - **Detalhes da piscina de armazenamento:** Mostra o estado da piscina de armazenamento, incluindo o tamanho total e restante do disco.

    ![Rever alocação de discos](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Na página **Escolha Método de Criação de Réplica,** especifique como pretende fazer a cópia de segurança inicial e, em seguida, clique em **Seguinte**.
   - O padrão é **automaticamente sobre a rede** e **agora**.
   - Se utilizar o padrão, recomendamos que especifique um tempo fora do pico. Escolha **Mais tarde** e especifique um dia e hora.
   - Para grandes quantidades de dados ou condições de rede menos ideais, considere replicar os dados offline utilizando suportes amovíveis.

    ![Escolha o método de criação de réplicas](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Nas **Opções de Verificação de Consistência**, selecione como e quando automatizar as verificações de consistência. Em seguida, clique em **Seguinte**.
      - Pode executar verificações de consistência quando os dados de réplica se tornam inconsistentes, ou num calendário definido.
      - Se não quiser configurar verificações automáticas de consistência, pode verificar manualmente. Para isso, clique com o botão direito no grupo de proteção > **Executar verificação de consistência**.

1. Na página **De Dados de Proteção Online,** selecione as pastas VM ou VM que pretende fazer o back up. Pode selecionar os membros individualmente ou clicar **em Selecionar Todos** para escolher todos os membros. Em seguida, clique em **Seguinte**.

    ![Especificar dados de proteção on-line](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na página **'Especificar agendamento' de cópia de segurança on-line,** especifique a frequência com que pretende fazer cópias de segurança do armazenamento local para o Azure.

    - Os pontos de recuperação da nuvem para os dados serão gerados de acordo com o calendário. Em seguida, clique em **Seguinte**.
    - Após a geração do ponto de recuperação, é transferido para o cofre dos Serviços de Recuperação em Azure.

    ![Especificar o horário de backup on-line](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na página **'Política de Retenção Online' especifique,** indique quanto tempo pretende manter os pontos de recuperação que são criados a partir das cópias de segurança diárias/semanais/mensais/ano para a Azure. em seguida, clique **em Seguinte**.

    - Não há limite de tempo para quanto tempo podes manter os dados em Azure.
    - O único limite é que não se pode ter mais de 9999 pontos de recuperação por instância protegida. Neste exemplo, o caso protegido é o servidor VMware.

    ![Especificar a política de retenção on-line](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na página **Resumo,** reveja as definições e, em seguida, clique em **Criar Grupo**.

    ![Membro do grupo de proteção e resumo de definição](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

Para apoiar a vSphere 6.7, faça o seguinte:

- Ativar TLS 1.2 no DPM Server

>[!NOTE]
>VMWare 6.7 em diante tinha TLS ativado como protocolo de comunicação.

- Desa estação de registo da seguinte forma:

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

## <a name="next-steps"></a>Passos seguintes

Para problemas de resolução de problemas ao configurar backups, reveja o [guia de resolução de problemas para o Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
