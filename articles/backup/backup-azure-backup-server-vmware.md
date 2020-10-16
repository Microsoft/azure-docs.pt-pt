---
title: Backup VMware VMs com Azure Backup Server
description: Neste artigo, aprenda a utilizar o Azure Backup Server para fazer backup vMware VMs em execução num servidor VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: db5e5c4bdac64e2faf5babb107ecec61a02d6468
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90069837"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Backup VMware VMs com Azure Backup Server

Este artigo explica como fazer backup VMware VMs em execução em VMware ESXi hosts/vCenter Server para Azure usando O Azure Backup Server (MABS).

Este artigo explica como:

- Crie um canal seguro para que o Azure Backup Server possa comunicar com servidores VMware através de HTTPS.
- Crie uma conta VMware que o Azure Backup Server utiliza para aceder ao servidor VMware.
- Adicione as credenciais de conta ao Azure Backup.
- Adicione o servidor vCenter ou ESXi ao Servidor de Backup Azure.
- Crie um grupo de proteção que contenha os VMware VMs que pretende fazer cópias de segurança, especifique as definições de backup e agende a cópia de segurança.

## <a name="supported-vmware-features"></a>Funcionalidades de VMware suportadas

O MABS fornece as seguintes funcionalidades ao fazer o backup de máquinas virtuais VMware:

- Cópia de segurança sem agente: O MABS não necessita de um agente para ser instalado no servidor vCenter ou ESXi, para fazer a cópia de segurança da máquina virtual. Em vez disso, basta fornecer o endereço IP ou o nome de domínio totalmente qualificado (FQDN), e credenciais de login usadas para autenticar o servidor VMware com MABS.
- Backup Integrado na Nuvem: O MABS protege as cargas de trabalho para o disco e para a nuvem. O fluxo de trabalho de backup e recuperação do MABS ajuda-o a gerir a retenção a longo prazo e o backup fora do local.
- Detetar e proteger VMs geridos por vCenter: O MABS deteta e protege os VM implantados num servidor VMware (servidor vCenter ou ESXi). À medida que o seu tamanho de implantação aumenta, use o vCenter para gerir o seu ambiente VMware. O MABS também deteta VMs geridos pelo vCenter, permitindo-lhe proteger grandes implementações.
- Proteção automática ao nível da pasta: vCenter permite organizar os seus VMs em pastas VM. O MABS deteta estas pastas e permite-lhe proteger os VMs ao nível da pasta e inclui todas as subpalhos. Ao proteger as pastas, o MABS não só protege os VMs nessa pasta, como também protege os VMs adicionados mais tarde. O MABS deteta diariamente novos VMs e protege-os automaticamente. Ao organizar os seus VMs em pastas recursivas, o MABS deteta e protege automaticamente os novos VM implantados nas pastas recursivas.
- O MABS protege os VMs armazenados num disco local, sistema de ficheiros de rede (NFS) ou armazenamento de clusters.
- O MABS protege os VMs migrados para o equilíbrio da carga: À medida que os VM são migrados para o equilíbrio de carga, o MABS deteta e continua a proteção contra o VM.
- O MABS pode recuperar ficheiros/pastas de um VM do Windows sem recuperar todo o VM, o que ajuda a recuperar os ficheiros necessários mais rapidamente.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

Antes de começar a fazer backup de uma máquina virtual VMware, reveja a seguinte lista de limitações e pré-requisitos.

- Se tiver usado o MABS para proteger um servidor vCenter (em execução no Windows) como servidor do Windows utilizando o FQDN do servidor, não pode proteger esse servidor vCenter como um servidor VMware utilizando o FQDN do servidor.
  - Pode utilizar o endereço IP estático do servidor vCenter como uma solução alternativa.
  - Se pretender utilizar o FQDN, deve parar a proteção como Servidor do Windows, remover o agente de proteção e, em seguida, adicionar como um VMware Server utilizando fQDN.
- Se utilizar o vCenter para gerir servidores ESXi no seu ambiente, adicione vCenter (e não ESXi) ao grupo de proteção MABS.
- Não é possível fazer cópias de segurança antes da primeira cópia de segurança do MABS. Assim que o MABS completar a primeira cópia de segurança, poderá fazer cópias de segurança.
- O MABS não pode proteger VMware VMs com discos de passagem e mapeamentos de dispositivos brutos físicos (pRDM).
- O MABS não consegue detetar ou proteger vApps VMware.
- O MABS não pode proteger VMware VMs com imagens existentes.

## <a name="before-you-start"></a>Antes de começar

- Verifique se está a executar uma versão do vCenter/ESXi que é suportada por cópias de segurança. Consulte aqui a [matriz](./backup-mabs-protection-matrix.md)de suporte.
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
  - [Obtenha ajuda](/system-center/dpm/create-dpm-protection-groups#figure-out-how-much-storage-space-you-need) para descobrir quanto espaço de armazenamento precisa. A informação é para DPM mas também pode ser usada para O Servidor de Backup Azure.

### <a name="set-up-the-certificate"></a>Configurar o certificado

Crie um canal seguro da seguinte forma:

1. No navegador do Azure Backup Server, insira o URL do cliente web vSphere. Se a página de login não aparecer, verifique as definições de procuração de ligação e navegador.

    ![vSphere Web Client](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na página de login do vSphere Web Client, selecione **Descarregue certificados de CA de raiz fidedignos**.

    ![Descarregue o certificado ca raiz fidedigno](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Um ficheiro chamado **download** é descarregado. Dependendo do seu navegador, recebe uma mensagem que pergunta se abre ou guarda o ficheiro.

    ![Baixar certificado ca](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Guarde o ficheiro na máquina Azure Backup Server com uma extensão .zip.

5. **Clique **download.zipà direita  >  **Extrate All**. O ficheiro .zip extrai o seu conteúdo para a pasta **certs,** que contém:
   - O ficheiro do certificado raiz com uma extensão que começa com uma sequência numerada como .0 e .1.
   - O ficheiro CRL tem uma extensão que começa com uma sequência como .r0 ou .r1. O ficheiro CRL está associado a um certificado.

    ![Certificados descarregados](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Na pasta **certs,** clique com o botão direito no ficheiro de certificado de raiz > **Renomeado .**

    ![Certificado de raiz de renome](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Altere a extensão do certificado de raiz para .crt, e confirme. O ícone do ficheiro muda para um que representa um certificado de raiz.

8. Clique com o botão direito no certificado raiz e no menu pop-up, **selecione Install Certificate**.

9. No **Certificado Import Wizard**, selecione Máquina **Local** como destino para o certificado e, em seguida, selecione **Seguinte**. Confirme se lhe perguntam se pretende permitir alterações no computador.

    ![Bem-vindo ao Feiticeiro](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Na página **Certificate Store,** selecione **Coloque todos os certificados na loja seguinte**e, em seguida, selecione Procurar para escolher a loja de **certificados.**

    ![Armazenamento de certificados](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. Na **Select Certificate Store**, selecione As **Autoridades de Certificação de Raiz Fidedigna como** a pasta de destino para os certificados e, em seguida, selecione **OK**.

    ![Pasta de destino certificado](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. Ao **completar o Assistente de Importação de Certificados,** verifique a pasta e, em seguida, selecione **Acabamento**.

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
2. No painel **Navigator,** selecione **Administração**.

    ![Administração](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Em **Funções de Administração,**  >  **Roles**selecione o ícone de função de adicionar (o símbolo +).

    ![Adicionar papel](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. In **Create Role**Role  >  **name**, *insin BackupAdminRole*. O nome pode ser o que quiser, mas deve ser reconhecível para o propósito do papel.

5. Selecione os privilégios como resumido na tabela abaixo e, em seguida, selecione **OK**.  O novo papel aparece na lista no painel **Roles.**
   - Selecione o ícone ao lado da etiqueta dos pais para expandir o progenitor e ver os privilégios da criança.
   - Para selecionar os privilégios VirtualMachine, você precisa ir a vários níveis para a hierarquia dos pais filhos.
   - Não precisas de selecionar todos os privilégios infantis dentro de um privilégio dos pais.

    ![Hierarquia do privilégio do filho-mãe](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Permissões de funções

A tabela que se segue captura os privilégios que precisa de atribuir à conta de utilizador que cria:

| Privilégios para conta de utilizador vCenter 6.5                          | Privilégios para conta de utilizador vCenter 6.7                            |
|----------------------------------------------------------------------------|----------------------------------------------------------------------------|
| Datastore cluster.Configure um cluster de datastore                           | Datastore cluster.Configure um cluster de datastore                           |
| Datastore.AlocarSpace                                                    | Datastore.AlocarSpace                                                    |
| Datastore.Navegue na loja de dados                                                 | Datastore.Navegue na loja de dados                                                 |
| Datastore.Operações de ficheiros de baixo nível                                        | Datastore.Operações de ficheiros de baixo nível                                        |
| Global.Métodos de desativação                                                     | Global.Métodos de desativação                                                     |
| Global.Permitir métodos                                                      | Global.Permitir métodos                                                      |
| Global.Licenças                                                            | Global.Licenças                                                            |
| Evento Global.Log                                                           | Evento Global.Log                                                           |
| Global.Gerir atributos personalizados                                            | Global.Gerir atributos personalizados                                            |
| Global.Definir atributo personalizado                                                | Global.Definir atributo personalizado                                                |
| Host.Operações locais. Criar máquina virtual                               | Host.Operações locais. Criar máquina virtual                               |
| Rede.Atribuir rede                                                     | Rede.Atribuir rede                                                     |
| Recurso. Atribuir máquina virtual ao pool de recursos                          | Recurso. Atribuir máquina virtual ao pool de recursos                          |
| vApp.Adicionar máquina virtual                                                   | vApp.Adicionar máquina virtual                                                   |
| vApp.Atribua o conjunto de recursos                                                  | vApp.Atribua o conjunto de recursos                                                  |
| vApp.Unregister                                                            | vApp.Unregister                                                            |
| VirtualMachine.Configuration. Adicionar ou remover dispositivo                         | VirtualMachine.Configuration. Adicionar ou remover dispositivo                         |
| Uration de machine.Configvirtual. Arrendamento em disco                                   | Uration de machine.Configvirtual. Adquirir arrendamento em disco                           |
| Uration de machine.Configvirtual. Adicionar disco novo                                 | Uration de machine.Configvirtual. Adicionar disco novo                                 |
| Uration de machine.Configvirtual. Avançado                                     | Uration de machine.Configvirtual. Configuração avançada                       |
| Uration de machine.Configvirtual. Rastreio de mudança de disco                         | Uration de machine.Configvirtual. Rastreio de mudança de disco toggle                  |
| Uration de machine.Configvirtual. Dispositivo USB anfitrião                              | Virtual machine.Configuration.Configure Host USB                    |
| Uration de machine.Configvirtual. Estender o disco virtual                          | Uration de machine.Configvirtual. Estender o disco virtual                          |
| Uration de machine.Configvirtual. Consulta de ficheiros não desnudos                          | Uration de machine.Configvirtual. Consulta de ficheiros não desnudos                          |
| Uration de machine.Configvirtual. Colocação de ficheiros de swap                           | Uration de machine.Configvirtual. Alterar colocação de swapfile                    |
| Máquina virtual. Execução do Programa de Operações de Hóspedes.Programa de Operação convidado         | Máquina virtual. Execução do Programa de Operações de Hóspedes.Programa de Operação convidado         |
| Máquina virtual. Operações de Hóspedes.Modificações da Operação de Hóspedes             | Máquina virtual. Operações de Hóspedes.Modificações da Operação de Hóspedes             |
| Máquina virtual. Operações de Hóspedes.Consultas de Operação de Hóspedes                   | Máquina virtual. Operações de Hóspedes.Consultas de Operação de Hóspedes                   |
| Máquina virtual. Interação . Ligação do dispositivo                            | Máquina virtual. Interação . Ligação do dispositivo                            |
| Máquina virtual. Interação . Gestão do sistema operativo convidado pela VIX API | Máquina virtual. Interação . Gestão do sistema operativo convidado pela VIX API |
| Máquina virtual. Interação . Desligar                                    | Máquina virtual. Interação . Desligar                                    |
| Máquina virtual. Inventário.Criar novo                                      | Máquina virtual. Inventário.Criar novo                                      |
| Máquina virtual. Inventário.Remover                                          | Máquina virtual. Inventário.Remover                                          |
| Máquina virtual. Inventário.Registo                                        | Máquina virtual. Inventário.Registo                                        |
| Máquina virtual. Provisioning.Permitir o acesso ao disco                            | Máquina virtual. Provisioning.Permitir o acesso ao disco                            |
| Máquina virtual. Provisioning.Permitir o acesso aos ficheiros                            | Máquina virtual. Provisioning.Permitir o acesso aos ficheiros                            |
| Máquina virtual. Provisioning.Permitir o acesso ao disco apenas de leitura                  | Máquina virtual. Provisioning.Permitir o acesso ao disco apenas de leitura                  |
| Máquina virtual. Provisioning.Permitir o download de máquinas virtuais               | Máquina virtual. Provisioning.Permitir o download de máquinas virtuais               |
| Máquina virtual. Gestão de instantâneos. Criar instantâneo                      | Máquina virtual. Gestão de instantâneos. Criar instantâneo                      |
| Máquina virtual. Gestão de instantâneos. Remover instantâneo                       | Máquina virtual. Gestão de instantâneos. Remover instantâneo                       |
| Máquina virtual. Gestão de instantâneos. Reverter para instantâneo                    | Máquina virtual. Gestão de instantâneos. Reverter para instantâneo                    |

> [!NOTE]
> A tabela que se segue lista os privilégios para as contas de utilizador vCenter 6.0 e vCenter 5.5.

| Privilégios para conta de utilizador vCenter 6.0 | Privilégios para conta de utilizador vCenter 5.5 |
| --- | --- |
| Datastore.AlocarSpace | Rede.Atribuir |
| Global.Gerir atributos personalizados | Datastore.AlocarSpace |
| Global.Definir atributo personalizado | VirtualMachine.Config. ChangeTracking |
| Host.Operações locais. Criar máquina virtual | VirtualMachine.State.RemoveSnapshot |
| A rede. Atribuir rede | VirtualMachine.State.CreateSnapshot |
| Recurso. Atribuir máquina virtual ao pool de recursos | VirtualMachine.Provisioning.DiskRandomRead |
| Uration de machine.Configvirtual. Adicionar disco novo | VirtualMachine.Interact.PowerOff |
| Uration de machine.Configvirtual. Avançado | VirtualMachine.Inventory.Create |
| Uration de machine.Configvirtual. Rastreio de mudança de disco | VirtualMachine.Config. AddNewDisk |
| Uration de machine.Configvirtual. Dispositivo USB anfitrião | VirtualMachine.Config. HostUSBDevice |
| Uration de machine.Configvirtual. Consulta de ficheiros não desnudos | VirtualMachine.Config. Config Avançado |
| Uration de machine.Configvirtual. Colocação de ficheiros de swap | VirtualMachine.Config. Troca de Substituição |
| Máquina virtual. Interação.Desligar | Global.ManageCustomFields |
| Máquina virtual. O inventário. Criar novo |   |
| Máquina virtual. Provisioning.Permitir o acesso ao disco |   |
| Máquina virtual. Provisões. Permitir o acesso ao disco apenas de leitura |   |
| Máquina virtual. Gestão de instantâneos. Criar instantâneo |   |
| Máquina virtual. Gestão de instantâneos. Remover instantâneo |   |

## <a name="create-a-vmware-account"></a>Criar uma conta VMware

1. No painel vCenter Server **Navigator,** selecione **Utilizadores e Grupos**. Se não utilizar o vCenter Server, crie a conta no anfitrião ESXi apropriado.

    ![Opção Utilizadores e Grupos](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Aparece o painel **de Utilizadores e Grupos do vCenter.**

2. No painel **vCenter Users and Groups,** selecione o **separador Utilizadores** e, em seguida, selecione o ícone de utilizadores de adicionar (o símbolo +).

    ![painel de utilizadores e grupos vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Na caixa de diálogo **do Novo Utilizador,** adicione as informações do utilizador > **OK**. Neste procedimento, o nome de utilizador é BackupAdmin.

    ![Nova caixa de diálogo do utilizador](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Para associar a conta de utilizador à função, no painel **Navigator,** selecione **Global Permissões**. No painel **Permissões Globais,** selecione o **separador 'Gerir'** e, em seguida, selecione o ícone de adicionar (o símbolo +).

    ![Painel de Permissões Globais](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. In **Global Permission Root - Add Permission**, selecione **Adicionar** para escolher o utilizador ou grupo.

    ![Escolha o utilizador ou grupo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Em **Selecionar Utilizadores/Grupos,** escolha **BackupAdmin**  >  **Add**. Nos **Utilizadores,** o formato *de nome de utilizador de domínio\username* é utilizado para a conta do utilizador. Se pretender utilizar um domínio diferente, escolha-o na lista **de Domínios.** Selecione **OK** para adicionar os utilizadores selecionados à caixa de diálogo **de permissão** de adicionar.

    ![Adicionar Backup UtilizadorAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. Em **Papel Atribuído**, a partir da lista de drop-down, selecione **BackupAdminRole**  >  **OK**.

    ![Atribuir o utilizador à função](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

No **separador 'Gerir'** no painel **De permissões Globais,** a nova conta de utilizador e a função associada aparecem na lista.

## <a name="add-the-account-on-azure-backup-server"></a>Adicione a conta no Azure Backup Server

1. Abra o servidor de backup Azure. Se não conseguir encontrar o ícone no ambiente de trabalho, abra o Microsoft Azure Backup da lista de aplicações.

    ![Ícone do servidor de backup Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Na consola Azure Backup **Management**Server, selecione  >   **Management Production Servers**Manage  >  **VMware**.

    ![Consola do servidor de backup Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. Na caixa de diálogo **'Gestão de Credenciais',** selecione **Adicionar**.

    ![Gerir caixa de diálogo de credenciais](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. In **Add Credential,** insira um nome e uma descrição para a nova credencial, e especifique o nome de utilizador e a palavra-passe que definiu no servidor VMware. O nome, *Credencial Contoso Vcenter* é usado para identificar a credencial neste procedimento. Se o servidor VMware e o Servidor de Backup Azure não estiverem no mesmo domínio, especifique o domínio no nome de utilizador.

    ![Azure Backup Server Adicionar caixa de diálogo credencial](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. **Selecione Adicionar** para adicionar a nova credencial.

    ![Adicionar novas credenciais](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Adicione o servidor vCenter

Adicione o servidor vCenter ao Servidor de Backup Azure.

1. Na consola Azure Backup **Management**Server, selecione  >  **Management Production Servers**  >  **Add**.

    ![Assistente de adição de servidor de produção aberto](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. No **Assistente de Adição do Servidor de Produção**  >  **Selecione** a página do tipo servidor de produção, selecione **VMware Servers**e, em seguida, selecione **Seguinte**.

    ![Assistente de adição de servidor de produção](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. No Nome do Servidor **de Computadores**  **Select/Endereço IP**, especifique o endereço FQDN ou IP do servidor VMware. Se todos os servidores ESXi forem geridos pelo mesmo vCenter, especifique o nome vCenter. Caso contrário, adicione o anfitrião ESXi.

    ![Especificar servidor VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Na **porta SSL,** entre na porta que é utilizada para comunicar com o servidor VMware. 443 é a porta predefinida, mas pode alterá-la se o seu servidor VMware ouvir numa porta diferente.

5. Em **Especificar Credencial,** selecione a credencial que criou anteriormente.

    ![Especificar credencial](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. **Selecione Adicionar** para adicionar o servidor VMware à lista de servidores. e selecione **Seguinte**.

    ![Adicionar servidor VMWare e credencial](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na página **Resumo,** **selecione Adicionar** para adicionar o servidor VMware ao Servidor de Backup Azure. O novo servidor é adicionado imediatamente, nenhum agente é necessário no servidor VMware.

    ![Adicione o servidor VMware ao Servidor de Backup do Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Verifique as definições na página **'Acabamento'.**

   ![Página de acabamento](./media/backup-azure-backup-server-vmware/summary-screen.png)

Se tiver vários anfitriões ESXi que não são geridos pelo servidor vCenter, ou se tiver várias instâncias do vCenter Server, tem de refazer o assistente para adicionar os servidores.

## <a name="configure-a-protection-group"></a>Configure um grupo de proteção

Adicione VMware VMs para cópia de segurança. Os grupos de proteção recolhem vários VMs e aplicam as mesmas definições de retenção de dados e backup a todos os VMs do grupo.

1. Na consola Azure Backup Server, selecione **Protection**, > **New**.

    ![Abra o assistente do Grupo criar novas proteções](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na página de boas-vindas **do grupo de proteção Criar Nova Proteção,** selecione **Seguinte**.

    ![Criar caixa de diálogo de assistente de novo grupo de proteção](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na página do **tipo do grupo Select Protection,** selecione **Servidores** e, em seguida, selecione **Seguinte**. Aparece a página **de membros do grupo Select.**

1. Nos **membros do grupo Select**, selecione as pastas VM (ou VM) que pretende fazer. e selecione **Seguinte**.

    - Quando selecionar uma pasta, ou VMs ou pastas no interior dessa pasta também são selecionados para cópia de segurança. Pode desmarcar pastas ou VMs que não quer fazer recuar.
1. Se um VM ou uma pasta já estiver a ser apoiado, não pode selecioná-lo. Isto garante que os pontos de recuperação duplicados não são criados para um VM.

    ![Selecione membros do grupo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na página **Select Data Protection Method,** insira um nome para o grupo de proteção e as definições de proteção. Para apoiar o Azure, desconfiem da proteção de curto prazo ao **Disk** e permitam a proteção online. e selecione **Seguinte**.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Em **Especificar Short-Term Objetivos**, especifique quanto tempo pretende manter os dados no disco.
   - No **Intervalo de Retenção**, especifique quantos dias devem ser mantidos pontos de recuperação do disco.
   - Na **frequência de sincronização,** especifique a frequência com que são tomados os pontos de recuperação do disco.
       - Se não quiser definir um intervalo de backup, pode verificar **antes de um ponto de recuperação** para que um backup seja executado antes de cada ponto de recuperação ser programado.
       - As cópias de segurança de curto prazo são cópias de segurança completas e não incrementais.
       - Selecione **Modificar** para alterar as horas/datas quando ocorrerem cópias de segurança a curto prazo.

         ![Especificar objetivos de curto prazo](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Na **Atribuição de Discos de Revisão,** reveja o espaço do disco previsto para as cópias de segurança VM. para os VMs.

   - As alocações recomendadas em disco baseiam-se na gama de retenção especificada, no tipo de carga de trabalho e no tamanho dos dados protegidos. Faça as alterações necessárias e, em seguida, selecione **Seguinte**.
   - **Tamanho dos dados:** Tamanho dos dados no grupo de proteção.
   - **Espaço em disco:** A quantidade recomendada de espaço em disco para o grupo de proteção. Se quiser modificar esta definição, deverá alocar um espaço total ligeiramente maior do que a quantidade que estima que cada fonte de dados cresce.
   - **Dados de colocação:** Se ligar a colocação, várias fontes de dados na proteção podem mapear para uma única réplica e volume de ponto de recuperação. A colocação não é suportada para todas as cargas de trabalho.
   - **Crescer automaticamente:** Se ligar esta definição, se os dados do grupo protegido ultrapassarem a alocação inicial, o Azure Backup Server tenta aumentar o tamanho do disco em 25%.
   - **Detalhes da piscina de armazenamento:** Mostra o estado da piscina de armazenamento, incluindo o tamanho total e restante do disco.

    ![Rever alocação de discos](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Na página **Escolha Método de Criação de Réplica,** especifique como pretende fazer a cópia de segurança inicial e, em seguida, selecione **Seguinte**.
   - O padrão é **automaticamente sobre a rede** e **agora**.
   - Se utilizar o padrão, recomendamos que especifique um tempo fora do pico. Escolha **Mais tarde** e especifique um dia e hora.
   - Para grandes quantidades de dados ou condições de rede menos ideais, considere replicar os dados offline utilizando suportes amovíveis.

    ![Escolha o método de criação de réplicas](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Nas **Opções de Verificação de Consistência**, selecione como e quando automatizar as verificações de consistência. e selecione **Seguinte**.
      - Pode executar verificações de consistência quando os dados de réplica se tornam inconsistentes, ou num calendário definido.
      - Se não quiser configurar verificações automáticas de consistência, pode verificar manualmente. Para isso, clique com o botão direito no grupo de proteção > **Executar verificação de consistência**.

1. Na página **De Dados de Proteção Online,** selecione as pastas VM ou VM que pretende fazer o back up. Pode selecionar os membros individualmente ou selecionar **Todos** para escolher todos os membros. e selecione **Seguinte**.

    ![Especificar dados de proteção on-line](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na página **'Especificar agendamento' de cópia de segurança on-line,** especifique a frequência com que pretende fazer cópias de segurança do armazenamento local para o Azure.

    - Os pontos de recuperação da nuvem para os dados serão gerados de acordo com o calendário. e selecione **Seguinte**.
    - Depois do ponto de recuperação ser gerado, é transferido para o cofre dos Serviços de Recuperação em Azure.

    ![Especificar o horário de backup on-line](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na página **'Política de Retenção Online' especifique,** indique quanto tempo pretende manter os pontos de recuperação que são criados a partir das cópias de segurança diárias/semanais/mensais/ano para a Azure. em seguida, selecione **Next**.

    - Não há limite de tempo para quanto tempo podes manter os dados em Azure.
    - O único limite é que não se pode ter mais de 9999 pontos de recuperação por instância protegida. Neste exemplo, o caso protegido é o servidor VMware.

    ![Especificar a política de retenção on-line](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na página **Resumo,** reveja as definições e, em seguida, selecione **Criar Grupo**.

    ![Membro do grupo de proteção e resumo de definição](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-parallel-backups"></a>Backups paralelos VMware

>[!NOTE]
> Esta funcionalidade é aplicável para MABS V3 UR1.

Com versões anteriores do MABS, as cópias de segurança paralelas foram realizadas apenas em grupos de proteção. Com o MABS V3 UR1, todas as cópias de segurança VMWare VMs dentro de um único grupo de proteção são paralelas, conduzindo a cópias de segurança VM mais rápidas. Todos os trabalhos de replicação delta VMWare são executados em paralelo. Por defeito, o número de postos de trabalho a executar em paralelo é fixado para 8.

Pode modificar o número de postos de trabalho utilizando a chave de registo como mostrado abaixo (não presente por padrão, precisa de adicioná-lo):

**Caminho-chave:**`Software\Microsoft\Microsoft Data Protection Manager\Configuration\ MaxParallelIncrementalJobs\VMWare`<BR>
**Tipo chave**: Valor DWORD (32 bits).

> [!NOTE]
> Pode modificar o número de empregos para um valor mais elevado. Se colocar o número de empregos em 1, os trabalhos de replicação funcionam em série. Para aumentar o número para um valor mais elevado, deve considerar o desempenho do VMWare. Considere o número de recursos em uso e o uso adicional exigido no VMWare vSphere Server, e determine o número de trabalhos de replicação delta para executar em paralelo. Além disso, esta alteração afetará apenas os grupos de proteção recém-criados. Para os grupos de proteção existentes, deve adicionar temporariamente mais um VM ao grupo de proteção. Isto deve atualizar a configuração do grupo de proteção em conformidade. Pode retirar este VM do grupo de proteção após a conclusão do procedimento.

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

Para apoiar a vSphere 6.7, faça o seguinte:

- Ativar TLS 1.2 no Servidor MABS

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

## <a name="exclude-disk-from-vmware-vm-backup"></a>Excluir o disco da cópia de segurança VMware VM

> [!NOTE]
> Esta funcionalidade é aplicável para MABS V3 UR1.

Com o MABS V3 UR1, pode excluir o disco específico da cópia de segurança VMware VM. O script de configuração **ExcludeDisk.ps1** está localizado no `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin folder` .

Para configurar a exclusão do disco, siga os passos abaixo:

### <a name="identify-the-vmware-vm-and-disk-details-to-be-excluded"></a>Identifique os detalhes VMWare VM e disk a excluir

  1. Na consola VMware, aceda às definições VM para as quais pretende excluir o disco.
  2. Selecione o disco que pretende excluir e observe o caminho para o disco.

        Por exemplo, para excluir o Disco Rígido 2 do TestVM4, o caminho para o Disco Rígido 2 é **[datastore1] TestVM4/TestVM4 \_ 1.vmdk**.

        ![Disco rígido a excluir](./media/backup-azure-backup-server-vmware/test-vm.png)

### <a name="configure-mabs-server"></a>Configure o servidor MABS

Navegue para o servidor MABS onde o VMware VMware está configurado para proteção para configurar a exclusão do disco.

  1. Obtenha os detalhes do anfitrião VMware que está protegido no servidor MABS.

        ```powershell
        $psInfo = get-DPMProductionServer
        $psInfo
        ```

        ```output
        ServerName   ClusterName     Domain            ServerProtectionState
        ----------   -----------     ------            ---------------------
        Vcentervm1                   Contoso.COM       NoDatasourcesProtected
        ```

  2. Selecione o anfitrião VMware e enuse a proteção VMs para o anfitrião VMware.

        ```powershell
        $vmDsInfo = get-DPMDatasource -ProductionServer $psInfo[0] -Inquire
        $vmDsInfo
        ```

        ```output
        Computer     Name     ObjectType
        --------     ----     ----------
        Vcentervm1  TestVM2      VMware
        Vcentervm1  TestVM1      VMware
        Vcentervm1  TestVM4      VMware
        ```

  3. Selecione o VM para o qual pretende excluir um disco.

        ```powershell
        $vmDsInfo[2]
        ```

        ```output
        Computer     Name      ObjectType
        --------     ----      ----------
        Vcentervm1   TestVM4   VMware
        ```

  4. Para excluir o disco, navegue na `Bin` pasta e execute o *ExcludeDisk.ps1* script com os seguintes parâmetros:

        > [!NOTE]
        > Antes de executar este comando, pare o serviço DPMRA no servidor MABS. Caso contrário, o script retorna o sucesso, mas não atualiza a lista de exclusão. Certifique-se de que não há empregos em curso antes de parar o serviço.

     **Para adicionar/remover o disco da exclusão, executar o seguinte comando:**

      ```powershell
      ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-Add|Remove] "[Datastore] vmdk/vmdk.vmdk"
      ```

     **Exemplo:**

     Para adicionar a exclusão do disco para TestVM4, executar o seguinte comando:

       ```powershell
      C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Add "[datastore1] TestVM4/TestVM4\_1.vmdk"
       ```

      ```output
       Creating C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\excludedisk.xml
       Disk : [datastore1] TestVM4/TestVM4\_1.vmdk, has been added to disk exclusion list.
      ```

  5. Verifique se o disco foi adicionado para exclusão.

     **Para visualizar a exclusão existente para VMs específicos, executar o seguinte comando:**

        ```powershell
        ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-view]
        ```

     **Exemplo**

        ```powershell
        C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -view
        ```

        ```output
        <VirtualMachine>
        <UUID>52b2b1b6-5a74-1359-a0a5-1c3627c7b96a</UUID>
        <ExcludeDisk>[datastore1] TestVM4/TestVM4\_1.vmdk</ExcludeDisk>
        </VirtualMachine>
        ```

     Uma vez configurada a proteção para este VM, o disco excluído não será listado durante a proteção.

        > [!NOTE]
        > Se estiver a executar estes passos para um VM já protegido, tem de executar a verificação de consistência manualmente depois de adicionar o disco para exclusão.

### <a name="remove-the-disk-from-exclusion"></a>Remover o disco da exclusão

Para remover o disco da exclusão, executar o seguinte comando:

```powershell
C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Remove "[datastore1] TestVM4/TestVM4\_1.vmdk"
```

## <a name="next-steps"></a>Passos seguintes

Para problemas de resolução de problemas ao configurar backups, reveja o [guia de resolução de problemas para o Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
