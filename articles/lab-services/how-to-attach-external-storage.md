---
title: Utilização de armazenamento de ficheiros externos em | de Serviços de Laboratório Microsoft Docs
description: Aprenda a criar um laboratório que use armazenamento de ficheiros externos nos Serviços de Laboratório.
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: 888e04db76567051f8c5eae7cf94c77e684cb146
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111793"
---
# <a name="using-external-file-storage-in-lab-services"></a>Utilização de armazenamento de ficheiros externos em Serviços de Laboratório

Este artigo cobrirá algumas das opções de armazenamento de ficheiros externos ao utilizar os Serviços Azure Lab.  [O Azure Files](https://azure.microsoft.com/services/storage/files/) oferece ações de ficheiros totalmente geridas na nuvem [acessível via SMB 2.1 e SMB 3.0.](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)  Uma partilha de Ficheiros Azure pode ser conectada publicamente ou privadamente dentro de uma rede virtual.  Além disso, pode ser configurado para usar as credenciais de AD do aluno para ligar à partilha de ficheiros.  A utilização de ficheiros Azure NetApp com volumes NFS para máquinas Linux é outra opção para armazenamento externo de ficheiros com serviços Azure Lab.  

## <a name="deciding-which-solution-to-use"></a>Decidir qual a solução a utilizar

Cada solução tem diferentes requisitos e capacidades.  A tabela abaixo lista pontos importantes a ter em conta para cada solução.  

|     Solução    |    Importante saber    |
| -------------- | ------------------------ |
| [Azure Files partilha com o ponto final público](#azure-files-share) | <ul><li>Todos têm acesso a leitura/escrita.</li><li>Não é necessário espreitar vnet.</li><li>Acessível a todos os VMs, não apenas VMs de laboratório.</li><li>Se utilizarem o Linux, os estudantes terão acesso à chave da conta de armazenamento.</li></ul> |
| [Azure Files compartilham com ponto final privado](#azure-files-share) | <ul><li>Todos têm acesso a leitura/escrita.</li><li>Vnet olhando necessário.</li><li>Acessível apenas a VMs na mesma rede (ou rede esprevada) como conta de armazenamento.</li><li>Se utilizarem o Linux, os alunos terão acesso à chave da Conta de Armazenamento.</li></ul> |
| [Ficheiros Azure com autorização de base de identidade](#azure-files-with-identity-base-authorization) | <ul><li>As permissões de acesso de leitura ou leitura/escrita podem ser definidas para pasta ou ficheiro.</li><li>Vnet olhando necessário.</li><li>A conta de armazenamento deve ser ligada ao Ative Directory.</li><li>Os VMs do laboratório devem estar unidos pelo domínio.</li><li>A chave da Conta de Armazenamento não é utilizada para os alunos ligarem-se à partilha de ficheiros.</li></ul> |
| [Ficheiros NetApp com volumes NFS](#netapp-files-with-nfs-volumes) | <ul><li>O acesso à leitura ou à leitura/escrita pode ser definido para volumes.</li><li>As permissões são definidas usando o endereço ip do aluno VM.</li><li>Vnet olhando necessário.</li><li>Pode ter de se registar para utilizar o serviço NetApp Files.</li><li>Só linux.</li></ul>

O custo de utilização do armazenamento externo não está incluído no custo da utilização dos Serviços Azure Lab.  Para obter mais informações sobre preços, consulte [os preços dos Ficheiros Azure](https://azure.microsoft.com/pricing/details/storage/files/) e [os preços dos Ficheiros Azure NetApp](https://azure.microsoft.com/pricing/details/netapp/).

## <a name="azure-files-share"></a>Partilha de Ficheiros Azure

As ações do Azure File são acedidas através de um ponto final público ou privado.  As ações do Ficheiro Azure são montadas usando a chave da conta de armazenamento como palavra-passe.  Com esta abordagem, todos têm acesso de leitura-escrita à partilha de ficheiros.

Se utilizar um ponto final público para a partilha dos Ficheiros Azure, é importante lembrar:

- A rede virtual para a conta de armazenamento não tem de ser espreitada para a conta do laboratório.  A partilha de ficheiros pode ser criada a qualquer momento antes da publicação do modelo VM.
- A partilha de ficheiros pode ser acedida a partir de qualquer máquina se um utilizador tiver a chave da conta de armazenamento.  
- Os alunos do Linux podem ver a chave da conta de armazenamento.  As credenciais para a montagem de uma partilha de Ficheiros Azure são armazenadas em `{file-share-name}.cred` VMs Linux e legíveis por sudo.  Uma vez que os alunos têm acesso sudo por padrão em VMs do Serviço Azure Lab, podem ler a chave da conta de armazenamento.  Se o ponto final da conta de armazenamento for público, os estudantes podem ter acesso à partilha de ficheiros fora do seu VM estudantil.  Considere rodar a chave da conta de armazenamento após o fim da aula e utilizar ações de ficheiros privados.

Se utilizar um ponto final privado para a partilha dos Ficheiros Azure, é importante lembrar:

- O acesso é restrito ao tráfego originário da rede privada e não pode ser acedido através da internet pública.  Apenas VMs na rede virtual privada, VMs em uma rede espreitada para a rede virtual privada ou máquinas ligadas a uma VPN para a rede privada podem aceder à partilha de ficheiros.  
- Os alunos do Linux podem ver a chave da conta de armazenamento.  As credenciais para a montagem de uma partilha de Ficheiros Azure são armazenadas em `{file-share-name}.cred` VMs Linux e legíveis por sudo.  Uma vez que os alunos têm acesso sudo por padrão em VMs do Serviço Azure Lab, podem ler a chave da conta de armazenamento.  Considere rodar a chave da conta de armazenamento após o fim da aula.
- Esta abordagem requer que a rede virtual de partilha de ficheiros seja espreitada para a conta do laboratório.  A rede virtual da conta de Armazenamento Azure deve ser espreitada para a rede virtual para a conta de laboratório **antes** da criação do laboratório.

> [!NOTE]
> As ações de ficheiros superiores a 5TB só estão disponíveis para [as contas [de armazenamento localmente redundante (LRS]].](/azure/storage/files/storage-files-how-to-create-large-file-share#restrictions)

Siga estes passos para criar um VM ligado a uma partilha de FicheiroS Azure.

1. Criar uma [conta de armazenamento Azure](/azure/storage/files/storage-how-to-create-file-share). Na página "método de conectividade", escolha o ponto final público ou o ponto final privado.
2. Se utilizar, crie um [ponto final privado](/azure/private-link/create-private-endpoint-storage-portal) para que as ações de ficheiros sejam acessíveis a partir da rede virtual.  Crie uma [zona de DNS privada](/azure/dns/private-dns-privatednszone) ou utilize uma existente. As zonas privadas de DNS do Azure fornecem resolução de nomes dentro de uma rede virtual.
3. Criar uma [partilha de ficheiros Azure](/azure/storage/files/storage-how-to-create-file-share). A partilha de ficheiros é acessível pelo nome de anfitrião público da conta de armazenamento.
4. Monte a partilha de ficheiros Azure no modelo VM:
    - [[Janelas]](/azure/storage/files/storage-how-to-use-files-windows)
    - [[Linux]](/azure/storage/files/storage-how-to-use-files-linux).  Consulte [a utilização de ficheiros Azure com o Linux](#using-azure-files-with-linux) para evitar problemas de montagem em VMs dos alunos.
5. [Publique](how-to-create-manage-template.md#publish-the-template-vm) o modelo VM.

> [!IMPORTANT]
> Certifique-se de que o Firewall não bloqueia a ligação SMB de saída através da porta 445. Por padrão, o SMB é permitido para VMs Azure.

### <a name="using-azure-files-with-linux"></a>Utilização de ficheiros Azure com Linux

Se utilizar as _instruções predefinidas_ para montar uma partilha de ficheiros Azure, a partilha de ficheiros parece desaparecer nos VMs do aluno uma vez que o modelo é publicado.  Abaixo está o script modificado que aborda esta questão.  

```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Se o modelo VM já estiver publicado que monte a partilha do Ficheiro Azure no `/mnt` diretório, o aluno pode fazer um dos seguintes.

- Mova a instrução para montar `/mnt` até ao topo do `/etc/fstab` ficheiro.  
- Modifique a instrução para montar `/mnt/{file-share-name}` em diretórios diferentes como `/prm-mnt/{file-share-name}` .

Os alunos devem correr `mount -a` para remontar os diretórios.

Para obter informações mais gerais sobre a utilização de ações de ficheiros com o Linux, consulte [a utilização de Ficheiros Azure com o Linux](/azure/storage/files/storage-how-to-use-files-linux).

## <a name="azure-files-with-identity-base-authorization"></a>Ficheiros Azure com autorização de base de identidade

As ações da Azure Files também podem ser acedidas usando a autenticação AD se

1. O estudante VM está unido ao domínio.
2. A autenticação AD está [ativada na Conta de Armazenamento Azure](/azure/storage/files/storage-files-active-directory-overview) que acolhe a partilha de ficheiros.  

A unidade de rede é montada na máquina virtual utilizando a identidade do utilizador, e não a chave da conta de armazenamento.  O acesso à conta de armazenamento pode ser utilizado em pontos finais públicos ou privados.

Vamos abordar alguns pontos importantes desta abordagem.

- As permissões podem ser definidas num diretório ou nível de ficheiro.
- As credenciais atuais do utilizador são utilizadas para autenticar a partilha de ficheiros.

Se utilizar um ponto final público para a partilha dos Ficheiros Azure, é importante lembrar:

- A rede virtual para a conta de armazenamento não tem de espreitar para a conta do laboratório.  A partilha de ficheiros pode ser criada a qualquer momento antes da publicação do modelo VM.

Se utilizar um ponto final privado para a partilha dos Ficheiros Azure, é importante lembrar:

- O acesso é restrito ao tráfego originário da rede privada e não pode ser acedido através da internet pública.  Apenas VMs na rede virtual privada, VMs em uma rede espreitada para a rede virtual privada ou máquinas ligadas a uma VPN para a rede privada podem aceder à partilha de ficheiros.  
- Esta abordagem requer que a rede virtual de partilha de ficheiros seja espreitada para a conta do laboratório.  A rede virtual da conta de Armazenamento Azure deve ser espreitada para a rede virtual para a conta de laboratório **antes** da criação do laboratório.

Siga os passos abaixo para criar uma autenticação AD ativada Azure Files partilhar e o domínio juntar-se aos VMs de laboratório.

1. Criar uma [conta de armazenamento Azure](/azure/storage/files/storage-how-to-create-file-share).
2. Se utilizar, crie um [ponto final privado](/azure/private-link/create-private-endpoint-storage-portal) para que as ações de ficheiros sejam acessíveis a partir da rede virtual.  Crie uma [zona de DNS privada](/azure/dns/private-dns-privatednszone) ou utilize uma existente. As zonas privadas de DNS do Azure fornecem resolução de nomes dentro de uma rede virtual.
3. Criar uma [partilha de ficheiros Azure](/azure/storage/files/storage-how-to-create-file-share).
4. Siga as medidas para permitir a autorização baseada na identidade.  Se utilizar a AD on-prem que está sincronizada com a Azure AD, siga as etapas para [a autenticação de Serviços de Domínio de Diretório Ativo no local sobre a SMB para ações de ficheiros Azure](/azure/storage/files/storage-files-identity-auth-active-directory-enable).  Se utilizar apenas o Azure AD, siga as etapas para permitir a [autenticação dos Serviços de Domínio do Diretório Ativo Azure nos Ficheiros Azure](/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable).
    >[!IMPORTANT]
    >Fale com a equipa que gere o seu AD para verificar se todos os pré-requisitos indicados nas instruções são cumpridos.
5. Atribua funções de permissão de partilha SMB no Azure.  Para mais informações sobre permissões concedidas a cada papel, consulte [permissões de nível de partilha](/azure/storage/files/storage-files-identity-ad-ds-assign-permissions).
    1. A função 'Dados de Ficheiros de Armazenamento SMB Share Elevated Contributor' deve ser atribuída à pessoa ou grupo que irá configurar permissões para o conteúdo da partilha de ficheiros.
    2. A função 'Data de Armazenamento SMB Share Contributor' deve ser atribuída a estudantes que necessitem de adicionar ou editar ficheiros na partilha de ficheiros.
    3. A função 'Data de Armazenamento SMB Share Reader' deve ser atribuída a estudantes que apenas precisarão de ler os ficheiros a partir da partilha de ficheiros.
6. Configurar permissões de nível de diretório/ficheiro para a partilha de ficheiros.  As permissões devem ser configuradas a partir de uma máquina de domínio que tenha acesso à rede à partilha de ficheiros.  **Para modificar permissões de diretório/nível de ficheiro, monte a partilha de ficheiros utilizando a chave de armazenamento, não as suas credenciais AAD.**  [Set-Acl](/powershell/module/microsoft.powershell.security/set-acl) O comando PowerShell ou [os icacls](/windows-server/administration/windows-commands/icacls) são as ferramentas de recomendação a utilizar ao atribuir permissões.
7. [Persa a rede virtual](how-to-connect-peer-virtual-network.md) para a conta de armazenamento na conta do laboratório.
8. [Crie o laboratório de sala de aula.](how-to-manage-classroom-labs.md)
9. Guarde um script no modelo VM que os alunos podem executar para ligar à unidade de rede.  Para obter o exemplo do script:
    1. Abra a conta de armazenamento no portal do Azure.
    1. Selecione **'Partilhas de Ficheiros'** no **serviço de ficheiros** no menu.
    1. Encontre a parte a que pretende ligar, selecione o botão elipses na extrema direita e escolha **Connect**.
    1. O **Connect** fly out abrirá com instruções para Windows, Linux e macOS.  Se utilizar o Windows, detenda o **método de autenticação** ao **Ative Directory**.
    1. Copie o código no exemplo e guarde-o na máquina do modelo num `.ps1` ficheiro para janelas ou `.sh` ficheiros para o Linux.
10. Na máquina de modelo, descarregue e execute script para [juntar máquinas de estudante ao domínio](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage).  O `Join-AzLabADTemplate` script [publicará automaticamente o modelo VM.](how-to-create-manage-template.md#publish-the-template-vm)  
    > [!NOTE]
    > A máquina do modelo não será unida ao domínio. Os instrutores devem atribuir um VM de estudante publicado para eles próprios para visualizar os ficheiros sobre a partilha.
11. Os estudantes que usam janelas podem ligar-se à partilha de Ficheiros Azure utilizando o [File Explorer](/azure/storage/files/storage-how-to-use-files-windows) com as suas credenciais uma vez dado o caminho para a partilha de ficheiros.  Alternadamente, os alunos podem executar o script criado acima para se conectarem à unidade de rede.  Para os alunos que usam Linux, execute o script criado acima.

## <a name="netapp-files-with-nfs-volumes"></a>Ficheiros NetApp com volumes NFS

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) é um serviço de armazenamento de ficheiros de classe empresarial, de alto desempenho e medido.

- As políticas de acesso podem ser definidas numa base por volume.
- As políticas de permissão são baseadas em IP para cada volume.
- Se os alunos precisam do seu próprio volume a que outros estudantes não têm acesso, as políticas de permissão devem ser atribuídas após a publicação do laboratório.
- No contexto dos Serviços Azure Lab, apenas as máquinas Linux são suportadas.
- A rede virtual para o conjunto de capacidades do Azure NetApp Files deve ser espreitada para a rede virtual para a conta de laboratório **antes** da criação do laboratório.

Siga os passos abaixo para utilizar uma participação dos Ficheiros Azure NetApp nos Serviços Azure Lab.

1. A bordo dos [Ficheiros Azure NetApp,](https://aka.ms/azurenetappfiles)se necessário.
2. Para criar um pool de capacidade netApp Files e volume(s NFS), consulte [configurar os Ficheiros Azure NetApp e o volume NFS](/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes).  Para obter informações sobre os níveis de serviço, consulte [os níveis de serviço para ficheiros Azure NetApp](/azure/azure-netapp-files/azure-netapp-files-service-levels).
3. [Perspeção da rede virtual](how-to-connect-peer-virtual-network.md) para o pool de capacidade dos Ficheiros NetApp para a conta de laboratório.
4. [Crie o laboratório de sala de aula.](how-to-manage-classroom-labs.md)
5. No modelo VM, instale os componentes necessários para utilizar as partilhas de ficheiros NFS.
    - Ubuntu:

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS

        ```bash
        sudo yum install nfs-utils
        ```

6. No modelo VM, guarde o script abaixo para `mount_fileshare.sh` montar a partilha de [Ficheiros NetApp](/azure/azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines).  Atribua a `capacity_pool_ipaddress` variável o endereço IP alvo de montagem para o pool de capacidade.  Obtenha as instruções de montagem para que o volume encontre o valor apropriado.  O script espera o caminho/nome do volume netApp Files.  Não se esqueça de correr `chmod u+x mount_fileshare.sh` para garantir que o script pode ser executado pelos utilizadores.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ may cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. Se todos os alunos estiverem a partilhar o acesso ao mesmo volume de Ficheiros NetApp, o `mount_fileshare.sh` script pode ser executado na máquina do modelo antes de ser publicado.  Se cada um tiver o seu próprio volume, guarde o guião para ser executado mais tarde pelo aluno.
8. [Publique](how-to-create-manage-template.md#publish-the-template-vm) o modelo VM.
9. [Configure a política](/azure/azure-netapp-files/azure-netapp-files-configure-export-policy) para a partilha de ficheiros.  A política de exportação pode permitir que um único VM ou vários VMs tenham acesso a um volume.  O acesso à leitura ou leitura/escrita pode ser concedido.
10. Os alunos devem iniciar o seu VM e executar o script para montar a partilha de ficheiros.  Só terão de publicar o guião uma vez.  O comando vai `./mount_fileshare.sh myvolumename` parecer.

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns para montar qualquer laboratório.

- [Criar e gerir um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)