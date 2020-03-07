---
title: Backup VMs vMware com servidor de backup Azure
description: Neste artigo, aprenda a usar o Servidor de Backup Azure para fazer backup vMware VMware vMware num servidor VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: df85cba42118a2e814a4a1c8338f3927e4d75f36
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392049"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Backup VMs vMware com servidor de backup Azure

Este artigo explica como fazer backup de VMware VMs em vMware ESXi hosts/vCenter Server para Azure usando o Servidor de Backup Azure.

Este artigo explica como:

- Configurar um canal seguro para que o Servidor de Backup Azure possa comunicar com servidores VMware através de HTTPS.
- Criar uma conta VMware que o Azure Backup Server utiliza para aceder ao servidor VMware.
- Adicione as credenciais de conta ao Azure Backup.
- Adicione o vCenter ou o servidor ESXi ao Servidor de Backup Azure.
- Configurar um grupo de proteção que contenha os VMware VMs que pretende fazer backup, especificar as definições de backup e agendar a cópia de segurança.

## <a name="before-you-start"></a>Antes de começar

- Verifique se está a executar uma versão do vCenter/ESXi que é suportada para cópia de segurança. Consulte a matriz de suporte [aqui](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix).
- Certifique-se de ter configurado o Servidor de Backup Azure. Se não o fez, [faça isso](backup-azure-microsoft-azure-backup.md) antes de começar. Deve estar a executar o Azure Backup Server com as últimas atualizações.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma ligação segura ao servidor vCenter

Por padrão, o Azure Backup Server comunica com servidores VMware através de HTTPS. Para configurar a ligação HTTPS, descarregue o certificado VMware Certificate Authority (CA) e importe-o no Servidor de Backup Azure.

### <a name="before-you-begin"></a>Antes de começar

- Se não quiser utilizar HTTPS, pode desativar a validação de [certificadoHTTPS para todos os servidores VMware](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Normalmente, liga-se de um navegador na máquina do Servidor de Backup Azure ao servidor vCenter/ESXi utilizando o VSphere Web Client. A primeira vez que fazes isto, a ligação não é segura e vai mostrar o seguinte.
- É importante entender como o Azure Backup Server lida com cópias de segurança.
  - Como primeiro passo, o Azure Backup Server reforça os dados para o armazenamento local do disco. O Azure Backup Server utiliza um pool de armazenamento, um conjunto de discos e volumes em que o Azure Backup Server armazena pontos de recuperação de discos para os seus dados protegidos. A piscina de armazenamento pode ser diretamente anexada ao armazenamento (DAS), um canal de fibra SAN, ou dispositivo de armazenamento iSCSI ou SAN. É importante garantir que tem armazenamento suficiente para backup local dos seus dados VMware VM.
  - O Azure Backup Server recua do armazenamento de discos locais para o Azure.
  - [Obtenha ajuda](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) para descobrir quanto espaço de armazenamento precisa. A informação é para DPM, mas também pode ser usada para o Servidor de Backup Azure.

### <a name="set-up-the-certificate"></a>Configurar o certificado

Criar um canal seguro da seguinte forma:

1. No navegador do Servidor de Backup Azure, introduza o URL do Cliente Web da vSphere. Se a página de login não aparecer, verifique as definições de procuração de ligação e de navegador.

    ![vSphere Web Client](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na página de login do vSphere Web Client, clique em **Baixar certificados CA de raiz fidedignas**.

    ![Descarregue o certificado ca de raiz fidedigna](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Um ficheiro chamado **download** é descarregado. Dependendo do seu navegador, recebe uma mensagem que pergunta se deve abrir ou guardar o ficheiro.

    ![Baixar certificado CA](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Guarde o ficheiro na máquina do Servidor de Backup Azure com uma extensão .zip.

5. Clique à direita **para baixar.zip** > **Extrato Tudo**. O ficheiro .zip extrai o seu conteúdo para a pasta **certs,** que contém:
   - O ficheiro do certificado de raiz com uma extensão que começa com uma sequência numerada como .0 e .1.
   - O ficheiro CRL tem uma extensão que começa com uma sequência como .r0 ou .r1. O ficheiro CRL está associado a um certificado.

    ![Certificados descarregados](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Na pasta **certs,** clique à direita no ficheiro do certificado raiz > **Rename**.

    ![Renomear certificado raiz](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Mude a extensão do certificado de raiz para .crt e confirme. O ícone do ficheiro muda para um que representa um certificado de raiz.

8. Clique no certificado raiz e no menu pop-up, selecione Certificado de **Instalação**.

9. No **Certificado Import Wizard,** selecione **Local Machine** como destino para o certificado e, em seguida, clique em **Seguinte**. Confirme se lhe perguntarem se pretende permitir alterações no computador.

    ![Bem-vindo feiticeiro](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Na página da Loja de **Certificados,** selecione **Colocar todos os certificados na loja seguinte,** e, em seguida, clique em **Navegar** para escolher a loja de certificados.

    ![Armazenamento de certificado](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. Na **Select Certificate Store,** selecione **Trusted Root Certification Authorities** como pasta de destino para os certificados e, em seguida, clique em **OK**.

    ![Pasta de destino de certificado](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. Ao **completar o Assistente de Importação de Certificado,** verifique a pasta e clique em **Terminar**.

    ![Verifique se o certificado está na pasta adequada](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Depois de confirmada a importação do certificado, inscreva-se no vCenter Server para confirmar que a sua ligação está segura.

### <a name="disable-https-certificate-validation"></a>Desativar a validação de certificadoS HTTPS

Se tiver limites seguros dentro da sua organização e não pretender utilizar o protocolo HTTPS entre os servidores VMware e a máquina de backup Do servidor de backup Azure, desative https da seguinte forma:

1. Copie e cole o seguinte texto num ficheiro .txt.

```text
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
"IgnoreCertificateValidation"=dword:00000001
```

2. Guarde o ficheiro na máquina do Servidor de Backup Azure com o nome **"DisableSecureAuthentication.reg**".

3. Clique duas vezes no ficheiro para ativar a entrada do registo.

## <a name="create-a-vmware-role"></a>Criar uma função VMware

O Servidor de Backup Azure precisa de uma conta de utilizador com permissões para aceder ao anfitrião V-Center Server/ESXi. Crie uma função VMware com privilégios específicos e, em seguida, associe uma conta de utilizador com a função.

1. Inscreva-se no vCenter Server (ou anfitrião ESXi se não estiver a utilizar o VCenter Server).
2. No painel **Navigator,** clique em **Administração**.

    ![Administração](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Na **Administração** > **Funções,** clique no ícone de função adicionar (o símbolo +).

    ![Adicionar papel](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. In **Create Role** > Role **name,** insira *BackupAdminRole*. O nome do papel pode ser o que quiser, mas deve ser reconhecível para o propósito do papel.

5. Selecione os privilégios como resumido na tabela abaixo e, em seguida, clique **OK**.  O novo papel aparece na lista no painel **Roles.**
   - Clique no ícone ao lado da etiqueta dos pais para expandir o progenitor e ver os privilégios da criança.
   - Para selecionar os privilégios VirtualMachine, precisa de ir a vários níveis para a hierarquia dos filhos-mãe.
   - Não precisas de selecionar todos os privilégios infantis dentro de um privilégio dos pais.

    ![Hierarquia do privilégio da criança-mãe](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Permissões de papéis

| **Privilégios para vCenter 6.5 e acima da conta de utilizador**        | **Privilégios para conta de utilizador vCenter 6.0**               | **Privilégios para conta de utilizador vCenter 5.5** |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------- |
| Datastore.AllocateSpace                                      |                                                           |                                             |
| Datastore.Navegue na loja de dados                                   | Datastore.AllocateSpace                                   | Rede.Atribuir                              |
| Datastore.Operações de ficheiros de baixo nível                          | Global.Gerir atributos personalizados                           | Datastore.AllocateSpace                     |
| Cluster de loja de dados. Configure um cluster de datatstore             | Global.set atributo personalizado                               | VirtualMachine.Config.ChangeTracking        |
| Global.Desativar métodos                                       | Host.Local operations. Criar máquina virtual              | VirtualMachine.State.RemoveSnapshot         |
| Global.Enable métodos                                        | A rede. Rede de atribuição                                   | VirtualMachine.State.CreateSnapshot         |
| Global.Licenças                                              | Um recurso. Atribuir máquina virtual ao conjunto de recursos         | VirtualMachine.Provisioning.DiskRandomRead  |
| Evento Global.Log                                             | Máquina virtual. Configuração.Adicione novo disco                | VirtualMachine.Interact.PowerOff            |
| Global.Gerir atributos personalizados                              | Máquina virtual. Configuração.Avançado                    | VirtualMachine.Inventory.Create             |
| Global.set atributo personalizado                                  | Máquina virtual. Configuração.Rastreio de alterações do disco        | VirtualMachine.Config.AddNewDisk            |
| Rede.Atribua rede                                       | Máquina virtual. Configuração.Dispositivo USB de hospedar             | VirtualMachine.Config.HostUSBDevice         |
| Um recurso. Atribuir máquina virtual ao conjunto de recursos            | Máquina virtual. Configuração.Consultas de ficheiros não possuídos         | VirtualMachine.Config.AdvancedConfig        |
| Máquina virtual. Configuração.Adicione novo disco                   | Máquina virtual. Configuração.Colocação de ficheiros de troca          | VirtualMachine.Config.SwapPlacement         |
| Máquina virtual. Configuração.Avançado                       | Máquina virtual. Interação.Desligar                     | Global.ManageCustomFields                   |
| Máquina virtual. Configuração.Rastreio de alterações do disco           | Máquina virtual. O inventário. Criar novo                     |                                             |
| Máquina virtual. Configuração.Aluguer de disco                     | Máquina virtual. Provisionamento.Permitir o acesso ao disco            |                                             |
| Máquina virtual. Configuração.Estender o disco virtual            | Máquina virtual. O provisionamento. Permitir o acesso ao disco apenas para leitura |                                             |
| Máquina virtual. Operações de Hóspedes.Modificações de Operação de Hóspedes | Máquina virtual. Gestão instantânea. Criar instantâneo       |                                             |
| Máquina virtual. Operações de Hóspedes.Execução do Programa de Operação Convidado | Máquina virtual. Gestão instantânea. Remover instantâneo       |                                             |
| Máquina virtual. Operações de Hóspedes.Consultas de Operação De Hóspedes     |                                                           |                                             |
| Máquina virtual. Interação. Ligação do dispositivo              |                                                           |                                             |
| Máquina virtual. Interação. Gestão do sistema operativo convidado pela VIX API |                                                           |                                             |
| Máquina virtual. Inventário.Registo                          |                                                           |                                             |
| Máquina virtual. Inventário.Remover                            |                                                           |                                             |
| Máquina virtual. Provisionamento.Permitir o acesso ao disco              |                                                           |                                             |
| Máquina virtual. Provisionamento.Permitir acesso adisco apenas para leitura    |                                                           |                                             |
| Máquina virtual. Provisionamento.Permitir o download de máquinavirtual |                                                           |                                             |
| Máquina virtual. Gestão instantânea. Criar instantâneo        |                                                           |                                             |
| Máquina virtual. Gestão instantânea. Remover instantâneo         |                                                           |                                             |
| Máquina virtual. Gestão instantânea. Reverter para instantâneo      |                                                           |                                             |
| vApp.Adicionar máquina virtual                                     |                                                           |                                             |
| vApp.Atribuir conjunto de recursos                                    |                                                           |                                             |
| vApp.Unregister                                              |                                                           |                                             |

## <a name="create-a-vmware-account"></a>Criar uma conta VMware

1. No painel VCenter Server **Navigator,** clique em **Utilizadores e Grupos**. Se não utilizar o VCenter Server, crie a conta no hospedeiro ESXi apropriado.

    ![Opção utilizadores e grupos](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    O painel **vCenter Users and Groups** aparece.

2. No painel **vCenter Utilizadores e Grupos,** selecione o separador **Utilizadores** e, em seguida, clique no ícone adicionar utilizadores (o símbolo +).

    ![painel de utilizadores e grupos vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Na caixa de diálogo **new user,** adicione as informações do utilizador > **OK**. Neste procedimento, o nome de utilizador é BackupAdmin.

    ![Nova caixa de diálogo do utilizador](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Para associar a conta de utilizador à função, no painel **Navigator,** clique em **Permissões Globais**. No painel **Permissões Globais,** selecione o separador **Gerir** e, em seguida, clique no ícone adicionar (o símbolo +).

    ![Painel de Permissões Globais](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. Na **Raiz de Permissão Global - Adicionar Permissão,** clique em **Adicionar** para escolher o utilizador ou grupo.

    ![Escolha utilizador ou grupo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Em **Selecionar Utilizadores/Grupos,** escolha **BackupAdmin** > **Adicionar**. Nos **Utilizadores,** o formato *domínio\username* é utilizado para a conta do utilizador. Se quiser utilizar um domínio diferente, escolha-o na lista **de Domínios.** Clique **em OK** para adicionar os utilizadores selecionados à caixa de diálogo Add **Permission.**

    ![Adicionar utilizador de BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. No **Papel Atribuído**, a partir da lista de drop-down, selecione **BackupAdminRole** > **OK**.

    ![Atribuir o utilizador ao papel](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

No separador **Gerir** no painel **De Permissões Globais,** a nova conta de utilizador e a função associada aparecem na lista.

## <a name="add-the-account-on-azure-backup-server"></a>Adicione a conta no Servidor de Backup Azure

1. Open Azure Backup Server. Se não conseguir encontrar o ícone no ambiente de trabalho, abra o Microsoft Azure Backup da lista de aplicações.

    ![Ícone do servidor de backup Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Na consola Do Servidor de Backup Azure, clique em **Management** >  **Production Servers** > **Manage VMware**.

    ![Consola Azure Backup Server](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. Na caixa de diálogo **'Gerir credenciais',** clique em **Adicionar**.

    ![Caixa de diálogo de gestão de credenciais do servidor de backup Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Em **Add Credential,** introduza um nome e uma descrição para a nova credencial e especifique o nome de utilizador e a palavra-passe definido no servidor VMware. O nome, *credencial Contoso Vcenter* é usado para identificar a credencial neste procedimento. Se o servidor VMware e o Servidor de Backup Azure não estiverem no mesmo domínio, especifique o domínio no nome do utilizador.

    ![Servidor de backup Azure Adicionar caixa de diálogo credencial](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Clique em **Adicionar** para adicionar a nova credencial.

    ![Caixa de diálogo de gestão de credenciais do servidor de backup Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Adicione o servidor vCenter

Adicione o servidor vCenter ao Servidor de Backup Azure.

1. Na consola Do Servidor de Backup Azure, clique em **Management** > **Production Servers** > **Add**.

    ![Assistente de adição de servidor de produção aberto](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. Na página de adição de **servidor** de produção > **selecione** a página do tipo servidor de produção, selecione **Servidores VMware**e, em seguida, clique em **Next**.

    ![Assistente de adição de servidor de produção](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. No select o**nome/endereço IP**dos **computadores,** especifique o endereço FQDN ou IP do servidor VMware. Se todos os servidores ESXi forem geridos pelo mesmo vCenter, especifique o nome vCenter. Caso contrário, adicione o hospedeiro ESXi.

    ![Especificar servidor VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Na **porta SSL,** introduza a porta que é usada para comunicar com o servidor VMware. 443 é a porta predefinida, mas pode trocá-la se o seu servidor VMware ouvir uma porta diferente.

5. Em **'Especificar Credencial', selecione**a credencial que criou anteriormente.

    ![Especificar credencial](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Clique em **Adicionar** para adicionar o servidor VMware à lista de servidores. Clique depois em **Seguinte**.

    ![Adicione o servidor VMWare e a credencial](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na página **Resumo,** clique em **Adicionar** para adicionar o servidor VMware ao Servidor de Backup Azure. O novo servidor é adicionado imediatamente, nenhum agente é necessário no servidor VMware.

    ![Adicione o servidor VMware ao Servidor de Backup Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Verifique as definições na página **'Fim'.**

   ![Página de acabamento](./media/backup-azure-backup-server-vmware/summary-screen.png)

Se tiver vários anfitriões ESXi que não são geridos pelo servidor vCenter, ou se tiver várias instâncias de vCenter Server, precisa de reexecutar o assistente para adicionar os servidores.

## <a name="configure-a-protection-group"></a>Configure um grupo de proteção

Adicione VMs VMware para cópia de segurança. Os grupos de proteção reúnem vários VMs e aplicam as mesmas definições de retenção de dados e backup a todos os VMs do grupo.

1. Na consola Azure Backup Server, clique em **Protection**, > **New**.

    ![Abra o assistente do Grupo de Proteção Nova](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na página de boas-vindas do grupo **de proteção Criar Novo Grupo de Proteção,** clique em **Next**.

    ![Criar nova caixa de diálogo de assistentes do Grupo de Proteção](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na página do **tipo select Protection,** selecione **Servers** e, em seguida, clique em **Next**. A página de **membros** do grupo Select aparece.

1. No **Select membros**do grupo, selecione os VMs (ou pastas VM) que pretende fazer de volta. Clique depois em **Seguinte**.

    - Quando selecionar uma pasta, ou VMs ou pastas dentro dessa pasta, também são selecionadas para cópia de segurança. Pode desverificar pastas ou VMs que não queira fazer recuar.
1. Se um VM ou pasta já estiver a ser apoiado, não pode selecioná-lo. Isto garante que os pontos de recuperação duplicados não são criados para um VM.

    ![Selecione membros do grupo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na página **Select Data Protection Method,** introduza um nome para o grupo de proteção e definições de proteção. Para fazer o apoio ao Azure, detete a proteção de curto prazo para **o Disco** e permita a proteção online. Clique depois em **Seguinte**.

    ![Selecione método de proteção de dados](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Em **Especificar Objetivos de Curto Prazo,** especifique quanto tempo pretende manter os dados apoiados no disco.
   - No **Intervalo de Retenção,** especifique quantos dias os pontos de recuperação do disco devem ser mantidos.
   - Na **frequência de sincronização,** especifique com que frequência são tomadas as capacidades de recuperação do disco.
       - Se não quiser definir um intervalo de backup, pode verificar antes de um ponto de **recuperação** para que um backup corra antes de cada ponto de recuperação estar agendado.
       - Backups de curto prazo são cópias de segurança completas e não incrementais.
       - Clique em **modificar** para alterar os horários/datas quando ocorrerem cópias de segurança de curto prazo.

         ![Especificar objetivos a curto prazo](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Na **Atribuição de Discos de Revisão,** reveja o espaço do disco fornecido para as cópias de segurança VM. para os VMs.

   - As dotações recomendadas para o disco baseiam-se na gama de retenção especificada, no tipo de carga de trabalho e no tamanho dos dados protegidos. Faça as alterações necessárias e, em seguida, clique em **Seguinte**.
   - **Tamanho dos dados:** Tamanho dos dados do grupo de proteção.
   - **Espaço em disco:** A quantidade recomendada de espaço em disco para o grupo de proteção. Se quiser modificar esta definição, deve alocar espaço total ligeiramente maior do que a quantidade que estima que cada fonte de dados cresça.
   - **Dados de colocação:** Se ligar a colocalização, várias fontes de dados na proteção podem mapear um único volume de réplica e ponto de recuperação. A colocalização não é suportada para todas as cargas de trabalho.
   - **Crescer automaticamente:** Se ligar esta definição, se os dados do grupo protegido superarem a alocação inicial, o Azure Backup Server tenta aumentar o tamanho do disco em 25%.
   - **Detalhes da piscina de armazenamento:** Mostra o estado do depósito, incluindo o tamanho total e restante do disco.

    ![Revisão da atribuição de disco](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Na página Escolha método de **criação** de réplicas, especifique como pretende pegar na cópia de segurança inicial e, em seguida, clique em **Seguinte**.
   - A predefinição está **automaticamente sobre a rede** e **agora**.
   - Se utilizar o predefinido, recomendamos que especifique um tempo fora do pico. Escolha **mais tarde** e especifique um dia e hora.
   - Para grandes quantidades de dados ou condições de rede menos do que ótimas, considere replicar os dados offline utilizando meios amovíveis.

    ![Escolha método de criação de réplicas](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Em **Opções de Verificação de Consistência,** selecione como e quando automatizar as verificações de consistência. Clique depois em **Seguinte**.
      - Pode executar verificações de consistência quando os dados da réplica se tornam inconsistentes, ou num horário definido.
      - Se não quiser configurar verificações automáticas de consistência, pode fazer uma verificação manual. Para isso, clique no grupo de proteção > **Execute a Verificação**de Consistência .

1. Na página **Desespecificação de Dados de Proteção Online,** selecione as pastas VMs ou VM que pretende fazer o backback. Pode selecionar os membros individualmente ou clicar **em Selecionar Todos** para escolher todos os membros. Clique depois em **Seguinte**.

    ![Especificar dados de proteção on-line](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na página **'Agenda', especificar** a frequência com que pretende fazer cópias de segurança dos dados do armazenamento local para o Azure.

    - Os pontos de recuperação da nuvem para os dados serão gerados de acordo com o calendário. Clique depois em **Seguinte**.
    - Após a geração do ponto de recuperação, é transferido para o cofre dos Serviços de Recuperação em Azure.

    ![Especificar o calendário de backup online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na página 'Especificar Política de **Retenção Online',** indique quanto tempo pretende manter os pontos de recuperação que são criados a partir das cópias de segurança diárias/semanais/mensais/anuais para o Azure. em seguida, clique **em Seguinte**.

    - Não há limite de tempo para quanto tempo pode manter os dados em Azure.
    - O único limite é que não se pode ter mais de 9999 pontos de recuperação por instância protegida. Neste exemplo, a instância protegida é o servidor VMware.

    ![Especificar a política de retenção online](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na página **Resumo,** reveja as definições e clique em **Criar Grupo**.

    ![Membro do grupo de proteção e resumo de definição](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

Para fazer o apoio à vSphere 6.7, faça o seguinte:

- Ativar TLS 1.2 no Servidor DPM

>[!NOTE]
>VMWare 6.7 em diante tinha TLS ativado como protocolo de comunicação.

- Desloque as teclas de registo da seguinte forma:

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

Para problemas de resolução de problemas ao configurar backups, reveja o guia de resolução de [problemas para o Servidor de Backup Azure](./backup-azure-mabs-troubleshoot.md).
