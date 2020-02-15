---
title: Configure grupos de disponibilidade para O Servidor SQL em máquinas virtuais RHEL em Máquinas Virtuais Azure - Linux [ Máquinas Virtuais Linux ] Microsoft Docs
description: Aprenda sobre a criação de alta disponibilidade num ambiente de cluster RHEL e instale a STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 01/27/2020
ms.openlocfilehash: 0eaff1685cea88d352f1a22f382b7af2ed0ed6cb
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252217"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Tutorial: Configure grupos de disponibilidade para SQL Server em máquinas virtuais RHEL em Azure 

> [!NOTE]
> O tutorial apresentado está em **pré-visualização pública.** 
>
> Utilizamos o SQL Server 2017 com RHEL 7.6 neste tutorial, mas é possível utilizar o SQL Server 2019 em RHEL 7 ou RHEL 8 para configurar ha. Os comandos para configurar os recursos do grupo de disponibilidade mudaram no RHEL 8, e você vai querer olhar para o artigo, [Criar recursos](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) de grupo de disponibilidade e recursos RHEL 8 para mais informações sobre os comandos corretos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Criar um novo grupo de recursos, Conjunto de Disponibilidade e Máquinas Virtuais Azure Linux (VM)
> - Ativar Alta Disponibilidade (HA)
> - Criar um cluster Pacemaker
> - Configure um agente de esgrima criando um dispositivo STONITH
> - Instale o SQL Server e as ferramentas mssql no RHEL
> - Configure o servidor SQL sempre no grupo de disponibilidade
> - Configure recursos do grupo de disponibilidade (AG) no cluster Pacemaker
> - Teste uma falha e o agente de esgrima

Este tutorial utilizará a interface de linha de comando Azure (CLI) para implantar recursos no Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se preferir instalar e utilizar o CLI localmente, este tutorial requer a versão Azure CLI 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um Grupo de Recursos

Se tiver mais de uma subscrição, [detete a subscrição](/cli/azure/manage-azure-subscriptions-azure-cli) para a qual pretende utilizar estes recursos.

Utilize o seguinte comando para criar um Grupo de Recursos `<resourceGroupName>` numa região. Substitua `<resourceGroupName>` com um nome à sua escolha. Estamos a usar `East US 2` para este tutorial. Para mais informações, consulte o seguinte [Quickstart](../quick-create-cli.md).

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

O próximo passo é criar um Conjunto de Disponibilidade. Execute o seguinte comando em Azure Cloud Shell e substitua `<resourceGroupName>` pelo nome do Grupo de Recursos. Escolha um nome para `<availabilitySetName>`.

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Deve obter os seguintes resultados assim que o comando terminar:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>Criar VMs RHEL dentro do conjunto de disponibilidade

> [!WARNING]
> Se escolher uma imagem RHEL Pay-As-You-Go (PAYG) e configurar a Alta Disponibilidade (HA), poderá ser obrigado a registar a sua subscrição. Isto pode fazer com que pague duas vezes pela subscrição, uma vez que será cobrado pela subscrição do Microsoft Azure RHEL para o VM, e uma subscrição da Red Hat. Para obter mais informações, consulte https://access.redhat.com/solutions/2458541.
>
> Para evitar ser "faturado duplo", use uma imagem RHEL HA ao criar o VM Azure. As imagens oferecidas como imagens RHEL-HA também são imagens PAYG com ha repo pré-habilitado.

1. Obtenha uma lista de imagens de Máquina Virtual (VM) que oferecem RHEL com HA:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    Deverá ver os seguintes resultados:

    ```output
    [
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.4",
              "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
              "version": "7.4.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.5",
              "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
              "version": "7.5.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.6",
              "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
              "version": "7.6.2019062019"
            }
    ]
    ```

    Para este tutorial, estamos a escolher a imagem `RedHat:RHEL-HA:7.6:7.6.2019062019`.

    > [!IMPORTANT]
    > Os nomes das máquinas devem ter menos de 15 caracteres para configurar o Grupo de Disponibilidade. O nome de utilizador não pode conter caracteres maiúsculos e as palavras-passe devem ter mais de 12 caracteres.

1. Queremos criar 3 VMs no Conjunto de Disponibilidade. Substitua o seguinte no comando abaixo:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>` - Um exemplo seria "Standard_D16_v3"
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

O comando acima cria os VMs, e cria um VNet padrão para esses VMs. Para obter mais informações sobre as diferentes configurações, consulte o artigo [az vm criar.](https://docs.microsoft.com/cli/azure/vm)

Deve obter resultados semelhantes aos seguintes, uma vez que o comando esteja concluído para cada VM:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> A imagem predefinida que é criada com o comando acima cria um disco OS de 32GB por padrão. Pode ficar sem espaço com esta instalação predefinida. Pode utilizar o seguinte parâmetro adicionado ao comando `az vm create` acima para criar um disco OS com 128GB como exemplo: `--os-disk-size-gb 128`.
>
> Em seguida, pode configurar o Gestor de [VolumeLógico (LVM)](../../../virtual-machines/linux/configure-lvm.md) se precisar de expandir os volumes de pastas apropriados para acomodar a sua instalação.

### <a name="test-connection-to-the-created-vms"></a>Ligação de teste aos VMs criados

Ligue-se a VM1 ou aos outros VMs utilizando o seguinte comando em Azure Cloud Shell. Se não conseguir encontrar os seus IPs VM, siga este [Quickstart na Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Se a ligação for bem sucedida, deverá ver a seguinte saída representando o terminal Linux:

```output
[<username>@<VM1> ~]$
```

Digite `exit` para sair da sessão sSH.

## <a name="enable-high-availability"></a>Ativar alta disponibilidade

> [!IMPORTANT]
> Para completar esta parte do tutorial, deve ter uma subscrição para rHEL e o Add-on de Alta Disponibilidade. Se estiver a utilizar uma imagem recomendada na secção anterior, não tem de registar outra subscrição.
 
Ligue-se a cada nó VM e siga o guia abaixo para ativar ha. Para mais informações, consulte [a subscrição de alta disponibilidade para o RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Será mais fácil se abrir uma sessão de SSH a cada um dos VMs simultaneamente, uma vez que os mesmos comandos terão de ser executados em cada VM ao longo do artigo.
>
> Se estiver a copiar e colar vários comandos `sudo`, e for solicitado uma senha, os comandos adicionais não serão executados. Executar cada comando separadamente.


1. Executar os seguintes comandos em cada VM para abrir as portas de firewall pacemaker:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Atualizar e instalar pacotes Pacemaker em todos os nós utilizando os seguintes comandos:

    > [!NOTE]
    > **O nmap** é instalado como parte deste bloco de comando como uma ferramenta para encontrar endereços IP disponíveis na sua rede. Não é preciso instalar **o nmap,** mas será útil mais tarde neste tutorial.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Detete a palavra-passe para o utilizador predefinido que é criado ao instalar pacotes Pacemaker. Use a mesma senha em todos os nós.

    ```bash
    sudo passwd hacluster
    ```

1. Utilize o seguinte comando para abrir o ficheiro dos anfitriões e configurar a resolução de nomes do anfitrião. Para mais informações, consulte [a Configure AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) sobre a configuração do ficheiro dos anfitriões.

    ```
    sudo vi /etc/hosts
    ```

    No **vi** editor, insira `i` para inserir texto, e numa linha em branco, adicione o **IP privado** do VM correspondente. Em seguida, adicione o nome VM após um espaço ao lado do IP. Cada linha deve ter uma entrada separada.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Recomendamos que utilize o seu endereço **IP privado** acima. A utilização do endereço IP público nesta configuração fará com que a configuração falhe e não recomendamos expor o seu VM a redes externas.

    Para sair do **editor vi,** primeiro acerte na chave **Esc,** e depois insira o comando `:wq` para escrever o ficheiro e desistir.

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster Pacemaker

Nesta secção, ativamos e iniciaremos o serviço pcsd e, em seguida, configuraremos o cluster. Para o SQL Server no Linux, os recursos de cluster não são criados automaticamente. Precisamos de ativar e criar os recursos do pacemaker manualmente. Para mais informações, consulte o artigo sobre a configuração de uma instância de [cluster de failover para RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Ativar e iniciar o serviço pcsd e Pacemaker

1. Mande os comandos em todos os nós. Estes comandos permitem que os nós voltem a juntar-se ao aglomerado após o reboot.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Remova qualquer configuração de cluster existente de todos os nós. Execute o seguinte comando:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. No nó principal, execute os seguintes comandos para configurar o cluster.

    - Ao executar o comando `pcs cluster auth` para autenticar os nós do cluster, será solicitado para obter uma senha. Introduza a palavra-passe para o utilizador **hacluster** criada anteriormente.

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Execute o seguinte comando para verificar se todos os nós estão on-line.

    ```bash
    sudo pcs status
    ```

    Se todos os nós estiverem on-line, verá uma saída semelhante à seguinte:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```

1. Detete os votos esperados no agrupamento ao vivo para 3. Este comando apenas afeta o cluster ao vivo e não altera os ficheiros de configuração.

    Em todos os nós, estabeleça os votos esperados com o seguinte comando:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Configure o agente de esgrima

Um dispositivo STONITH fornece um agente de esgrima. As instruções abaixo são modificadas para este tutorial. Para mais informações, consulte [a criação de um dispositivo STONITH](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Verifique a versão do Agente de Cerca azure para se certificar de que está atualizado](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Utilize o seguinte comando:

```bash
sudo yum info fence-agents-azure-arm
```

Deve ver uma saída semelhante ao exemplo abaixo.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Registe uma nova aplicação no Diretório Ativo do Azure
 
 1. Ir para https://portal.azure.com
 2. Abra a lâmina do [Diretório Ativo Azure.](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) Vá para propriedades e anote o ID de diretório. Este é o `tenant ID`
 3. Clique nas [ **inscrições da App**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Clique em **Novo Registo**
 5. Insira um **Nome** como `<resourceGroupName>-app`, selecione **Contas apenas neste diretório** de organização
 6. Selecione Application Type **Web**, introduza um URL de iniciar sessão (por exemplo, http://localhost) e clique em Adicionar. O URL de início de sessão não é utilizado e pode ser qualquer URL válido
 7. Selecione **Certificados e segredos**e, em seguida, clique em **novo segredo de cliente**
 8. Introduza uma descrição para uma nova chave (segredo de cliente), selecione **Nunca expira** e clique em **Adicionar**
 9. Escreva o valor do segredo. Ele é usado como a senha para a entidade de serviço
10. Selecione **Descrição geral**. Anote o ID da aplicação. É usado como nome de utilizador (ID de login nos passos abaixo) do Diretor de Assistência
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Criar um papel personalizado para o agente da cerca

Siga o tutorial para [Criar uma função personalizada para os recursos Azure utilizando o Azure CLI](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

O seu ficheiro json deve ser semelhante ao seguinte:

- Substitua `<username>` por um nome à sua escolha. Isto é para evitar qualquer duplicação ao criar esta definição de papel.
- Substitua `<subscriptionId>` com o seu ID de subscrição Azure.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

Para adicionar o papel, executar o seguinte comando:

- Substitua `<filename>` com o nome do ficheiro.
- Se estiver a executar o comando de um caminho diferente da pasta a que o ficheiro é guardado, inclua o caminho da pasta do ficheiro no comando.

```bash
az role definition create --role-definition "<filename>.json"
```

Deverá ver o resultado seguinte:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>Atribuir o papel personalizado ao Diretor de Serviço

Atribuir o papel personalizado `Linux Fence Agent Role-<username>` que foi criado no último passo para o Diretor de Serviço. Não utilize a função de proprietário mais!
 
1. Ir para https://portal.azure.com
2. Abra a [lâmina de todos os recursos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Selecione a máquina virtual do primeiro nó de cluster
4. Clique no **controlo de acesso (IAM)**
5. Clique **em Adicionar uma atribuição de funções**
6. Selecione o papel `Linux Fence Agent Role-<username>` da lista **de papéis**
7. Na lista **Select,** introduza o nome da aplicação acima criada, `<resourceGroupName>-app`
8. Clicar em **Guardar**
9. Repita os passos acima para o nó de todos os aglomerados.

### <a name="create-the-stonith-devices"></a>Criar os dispositivos STONITH

Executar os seguintes comandos no nó 1:

- Substitua o `<ApplicationID>` pelo valor de identificação do registo de inscrição na sua candidatura.
- Substitua o `<servicePrincipalPassword>` pelo valor do segredo do cliente.
- Substitua o `<resourceGroupName>` pelo Grupo de Recursos da sua subscrição utilizada para este tutorial.
- Substitua o `<tenantID>` e o `<subscriptionId>` da sua Assinatura Azure.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Uma vez que já adicionámos uma regra à nossa firewall para permitir o serviço HA –`--add-service=high-availability`), não há necessidade de abrir as seguintes portas de firewall em todos os nós: 2224, 3121, 21064, 5405. No entanto, se estiver a ter algum tipo de problemas de ligação com ha, use o seguinte comando para abrir estas portas que estão associadas com HA.

> [!TIP]
> Você pode opcionalmente adicionar todas as portas deste tutorial de uma só vez para economizar algum tempo. As portas que precisam de ser abertas são explicadas nas suas secções relativas abaixo. Se quiser adicionar todos os portos agora, adicione os portos adicionais: 1433 e 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Instale o SQL Server e as ferramentas mssql
 
Utilize a secção abaixo para instalar o SQL Server e as ferramentas mssql nos VMs. Execute cada uma destas ações em todos os nós. Para mais informações, consulte [instalar o SQL Server num VM do Chapéu Vermelho](/sql/linux/quickstart-install-connect-red-hat).

### <a name="installing-sql-server-on-the-vms"></a>Instalação do Servidor SQL nos VMs

Os seguintes comandos são utilizados para instalar o Servidor SQL:

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

### <a name="open-firewall-port-1433-for-remote-connections"></a>Porta de firewall aberta 1433 para ligações remotas

Terá de abrir a porta 1433 do VM para se ligar remotamente. Utilize os seguintes comandos para abrir a porta 1433 na firewall de cada VM:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Instalação de ferramentas de linha de comando sQL Server

Os seguintes comandos são utilizados para instalar ferramentas de linha de comando SQL Server. Para mais informações, consulte [a instalação das ferramentas de linha de comando sQL Server](/sql/linux/quickstart-install-connect-red-hat#tools).

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Para conveniência, adicione /opt/mssql-tools/bin/ à variável ambiente PATH. Isto permite-lhe executar as ferramentas sem especificar o caminho completo. Executar os seguintes comandos para modificar o PATH para sessões de login e sessões interativas/não-login:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Verifique o estado do Servidor SQL

Uma vez terminada a configuração, pode verificar o estado do Servidor SQL e verificar se está em execução:

```bash
systemctl status mssql-server --no-pager
```

Deverá ver o resultado seguinte:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-sql-server-always-on-availability-group"></a>Configure o servidor SQL sempre no grupo de disponibilidade

Utilize os seguintes passos para configurar o Grupo De Disponibilidade do Servidor SQL sempre em disponibilidade para os seus VMs. Para mais informações, consulte [configure SQL Server Always On Availability Group para uma alta disponibilidade no Linux](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-alwayson-availability-groups-and-restart-mssql-server"></a>Ativar os grupos de disponibilidade AlwaysOn e reiniciar o servidor mssql

Ativar os grupos de disponibilidade AlwaysOn em cada nó que acolhe uma instância do Servidor SQL. Em seguida, reiniciar o servidor mssql. Execute o seguintes script:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Criar um certificado

Atualmente não apoiamos a autenticação aditiva para o ponto final da AG. Portanto, temos de usar um certificado para encriptação de ponto final da AG.

1. Ligue-se a **todos os nós** utilizando o SQL Server Management Studio (SSMS) ou o SQL CMD. Executar os seguintes comandos para permitir a sessão AlwaysOn_health e criar uma chave mestra:

    > [!IMPORTANT]
    > Se estiver a ligar-se remotamente à sua instância do SQL Server, terá de ter a porta 1433 aberta na sua firewall. Também terá de permitir ligações de entrada à porta 1433 no seu NSG para cada VM. Para mais informações, consulte [Criar uma regra](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) de segurança para criar uma regra de segurança de entrada.

    - Substitua o `<Master_Key_Password>` pela sua própria senha.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Ligue-se à réplica primária utilizando SSMS ou SQL CMD. Os comandos abaixo criarão um certificado em `/var/opt/mssql/data/dbm_certificate.cer` e uma chave privada em `var/opt/mssql/data/dbm_certificate.pvk` na sua réplica principal do SQL Server:

    - Substitua o `<Private_Key_Password>` pela sua própria senha.

```sql
CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
GO
BACKUP CERTIFICATE dbm_certificate
   TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
   WITH PRIVATE KEY (
           FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
           ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
       );
```

Saia da sessão CmD SQL executando o comando `exit` e volte à sua sessão SSH.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Copie o certificado para as réplicas secundárias e crie os certificados no servidor

1. Copie os dois ficheiros que foram criados para a mesma localização em todos os servidores que irão acolher réplicas de disponibilidade.
 
    No servidor principal, execute o seguinte comando `scp` para copiar o certificado para os servidores-alvo:

    - Substitua `<username>` e `<VM2>` pelo nome de utilizador e nome VM de destino que está a utilizar.
    - Execute este comando para todas as réplicas secundárias.

    > [!NOTE]
    > Não tens de correr `sudo -i`, o que te dá o ambiente de raiz. Podias apenas executar o comando `sudo` em frente a cada comando, como fizemos anteriormente neste tutorial.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. No servidor alvo, execute o seguinte comando:

    - Substitua `<username>` pelo seu nome de utilizador.
    - O comando `mv` move os ficheiros ou diretórios de um lugar para outro.
    - O comando `chown` é usado para alterar o proprietário e grupo de ficheiros, diretórios ou links.
    - Execute estes comandos para todas as réplicas secundárias.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. O seguinte script Transact-SQL cria um certificado a partir da cópia de segurança que criou na réplica primária do SQL Server. Atualize o script com senhas fortes. A palavra-passe de desencriptação é a mesma palavra-passe que usou para criar o ficheiro .pvk no passo anterior. Para criar o certificado, execute o seguinte script utilizando SQL CMD ou SSMS em todos os servidores secundários:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Crie os pontos finais espelhados da base de dados em todas as réplicas

Executar o seguinte script em todas as instâncias SQL usando SQL CMD ou SSMS:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
```

### <a name="create-the-availability-group"></a>Criar o grupo de disponibilidade

Ligue-se à instância do Servidor SQL que acolhe a réplica primária utilizando O SQL CMD ou o SSMS. Executar o seguinte comando para criar o Grupo de Disponibilidade:

- Substitua `ag1` pelo nome do Grupo de Disponibilidade pretendido.
- Substitua os valores `<VM1>`, `<VM2>`e `<VM3>` pelos nomes dos casos do Servidor SQL que acolhem as réplicas.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Criar um login do Servidor SQL para Pacemaker

Em todos os servidores SQL, crie um login SQL para Pacemaker. O seguinte Transact-SQL cria um login.

- Substitua `<password>` pela sua própria senha complexa.

```sql
USE [master]
GO
CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO
ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
```

Em todos os Servidores SQL, guarde as credenciais utilizadas para o login do Servidor SQL. 

1. Criar o ficheiro:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Adicione as seguintes 2 linhas ao ficheiro:

    ```bash
    pacemakerLogin
    <password>
    ```

    Para sair do **editor vi,** primeiro acerte na chave **Esc,** e depois insira o comando `:wq` para escrever o ficheiro e desistir.

1. Tornar o ficheiro apenas legível por raiz:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Junte-se a réplicas secundárias ao grupo de disponibilidade

1. Para se juntar às réplicas secundárias à AG, terá de abrir a porta 5022 na firewall para todos os servidores. Executar o seguinte comando na sua sessão SSH:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. Nas suas réplicas secundárias, execute os seguintes comandos para se juntar a eles à AG:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    ```

1. Executar o seguinte script Transact-SQL na réplica primária e cada réplica secundária:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    ```

1. Uma vez que as réplicas secundárias são unidas, você pode vê-las no SSMS Object Explorer expandindo o nó **sempre em alta disponibilidade:**

    ![disponibilidade-grupo-joined.png](media/sql-server-linux-rhel-ha-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Adicione uma base de dados ao grupo de disponibilidade

Seguiremos o artigo do grupo de [disponibilidade configurar sobre](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group)a adição de uma base de dados .

Os seguintes comandos Transact-SQL são utilizados neste passo. Execute estes comandos na réplica primária:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Verifique se a base de dados é criada nos servidores secundários

Em cada réplica secundária do SQL Server, execute a seguinte consulta para ver se a base de dados db1 foi criada e está em um estado SINCRONIZADO:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Se a lista de `synchronization_state_desc` sincronizada para `db1`, isto significa que as réplicas são sincronizadas. Os secundários mostram `db1` na réplica primária.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Criar recursos de grupo de disponibilidade no cluster Pacemaker

Seguiremos o guia para criar os recursos do grupo de [disponibilidade no cluster Pacemaker.](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)

### <a name="create-the-ag-cluster-resource"></a>Criar o recurso de cluster AG

1. Utilize o seguinte comando para criar o recurso `ag_cluster` no grupo de disponibilidade `ag1`.

    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

1. Verifique o seu recurso e certifique-se de que estão on-line antes de proceder utilizando o seguinte comando:

    ```bash
    sudo pcs resource
    ```

    Deverá ver o resultado seguinte:

    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM1>
    ```

### <a name="create-a-virtual-ip-resource"></a>Criar um recurso IP virtual

1. Utilize um endereço IP estático disponível da sua rede para criar um recurso IP virtual. Pode encontrar um utilizando a ferramenta de comando `nmap`.

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

1. Definir a propriedade **ativada por stonith** para falso

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

1. Criar o recurso IP virtual utilizando o seguinte comando:

    - Substitua o valor `<availableIP>` abaixo por um endereço IP não utilizado.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Adicionar Constrangimentos

1. Para garantir que o endereço IP e o recurso AG estão a funcionar no mesmo nó, deve ser configurada uma restrição de colocalização. Execute o seguinte comando:

    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

1. Crie um constrangimento de encomenda para garantir que o recurso AG está a funcionar antes do endereço IP. Embora a restrição de co-localização implique uma restrição de ordenação, isto a impõe.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

1. Para verificar os constrangimentos, execute o seguinte comando:

    ```bash
    sudo pcs constraint list --full
    ```

    Deverá ver o resultado seguinte:

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Reativar o stonith

Estamos prontos para os testes. Reativar o stonith no cluster executando o seguinte comando no nó 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>Verificar o estado do cluster

Pode verificar o estado dos seus recursos de cluster utilizando o seguinte comando:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Ativação pós-falha de teste

Para garantir que a configuração foi bem sucedida até agora, vamos testar uma falha. Para mais informações, consulte [Sempre on Availability Group failover no Linux](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Executar o seguinte comando para falhar manualmente a réplica primária para `<VM2>`. Substitua `<VM2>` pelo valor do seu nome de servidor.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Se verificar novamente os seus constrangimentos, verá que outra restrição foi adicionada devido à falha manual:

    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

1. Retire a restrição com `cli-prefer-ag_cluster-master` de identificação utilizando o seguinte comando:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Verifique os seus recursos de cluster utilizando o comando `sudo pcs resource`, e deve ver que a instância primária está agora `<VM2>`.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
     
    ```

## <a name="test-fencing"></a>Esgrima de Teste

Pode testar o STONITH executando o seguinte comando. Tente executar o comando abaixo do `<VM1>` para `<VM3>`.

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Por defeito, a ação da cerca traz o nó para fora e, em seguida, ligado. Se quiser apenas desligar o nó, use a opção `--off` no comando.

Deve obter a seguinte saída:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Para obter mais informações sobre o teste de um dispositivo de cerca, consulte o seguinte artigo da [Red Hat.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar)

## <a name="next-steps"></a>Passos seguintes

Para utilizar um Listener de Grupo de Disponibilidade para os seus Servidores SQL que criou no Azure, primeiro terá de criar e configurar um equilibrador de carga.

> [!div class="nextstepaction"]
> [Criar e configurar o equilibrador de carga no portal Azure](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal)
