---
title: Configurar grupos de disponibilidade para SQL Server em máquinas virtuais RHEL em Azure - Máquinas virtuais Linux Microsoft Docs
description: Saiba como criar uma elevada disponibilidade num ambiente de cluster RHEL e criar o STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 06/25/2020
ms.openlocfilehash: 4411bd490ab72aa27fbf16a8598a9ff0dae7a5b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358955"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Tutorial: Configurar grupos de disponibilidade para SQL Server em máquinas virtuais RHEL em Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> Utilizamos o SQL Server 2017 com RHEL 7.6 neste tutorial, mas é possível utilizar o SQL Server 2019 em RHEL 7 ou RHEL 8 para configurar alta disponibilidade. Os comandos para configurar o cluster pacemake e os recursos do grupo de disponibilidade mudaram em RHEL 8, e você vai querer olhar para o artigo [Criar recursos de grupo de disponibilidade](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) e recursos RHEL 8 para mais informações sobre os comandos corretos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Criar um novo grupo de recursos, conjunto de disponibilidade e máquinas virtuais Linux (VMs)
> - Ativar a alta disponibilidade (HA)
> - Criar um cluster pacemaker
> - Configure um agente de esgrima criando um dispositivo STONITH
> - Instale o SQL Server e as ferramentas mssql no RHEL
> - Configure o servidor SQL sempre no grupo de disponibilidade
> - Configure recursos do grupo de disponibilidade (AG) no cluster Pacemaker
> - Teste um failover e o agente de esgrima

Este tutorial usará o CLI Azure para implantar recursos em Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se preferir instalar e utilizar o CLI localmente, este tutorial requer a versão 2.0.30 ou posterior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se tiver mais do que uma subscrição, [desloque a subscrição](/cli/azure/manage-azure-subscriptions-azure-cli) para a qual pretende implementar estes recursos.

Utilize o seguinte comando para criar um grupo de recursos `<resourceGroupName>` numa região. `<resourceGroupName>`Substitua-o por um nome à sua escolha. Estamos a usar `East US 2` para este tutorial. Para mais informações, consulte o seguinte [Quickstart](../../../application-gateway/quick-create-cli.md).

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

O próximo passo é criar um conjunto de disponibilidade. Executar o seguinte comando em Azure Cloud Shell e substituir `<resourceGroupName>` pelo nome do seu grupo de recursos. Escolha um nome para `<availabilitySetName>` .

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Deverá obter os seguintes resultados assim que o comando estiver concluído:

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
> Se escolher uma imagem RHEL Pay-As-You-Go (PAYG) e configurar alta disponibilidade (HA), poderá ser obrigado a registar a sua subscrição. Isto pode fazer com que pague duas vezes pela subscrição, uma vez que será cobrado pela subscrição RHEL do Microsoft Azure para o VM e uma subscrição do Red Hat. Para obter mais informações, consulte https://access.redhat.com/solutions/2458541.
>
> Para evitar ser "faturado duplo", utilize uma imagem RHEL HA ao criar o Azure VM. As imagens oferecidas como imagens RHEL-HA também são imagens PAYG com ha repo pré-activadas.

1. Obtenha uma lista de imagens de máquinas virtuais que oferecem RHEL com HA:

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
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.0",
    "urn": "RedHat:RHEL-HA:8.0:8.0.2020021914",
    "version": "8.0.2020021914"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.1",
    "urn": "RedHat:RHEL-HA:8.1:8.1.2020021914",
    "version": "8.1.2020021914"
          },
          {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "80-gen2",
    "urn": "RedHat:RHEL-HA:80-gen2:8.0.2020021915",
    "version": "8.0.2020021915"
           },
           {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "81_gen2",
    "urn": "RedHat:RHEL-HA:81_gen2:8.1.2020021915",
    "version": "8.1.2020021915"
           }
    ]
    ```

    Para este tutorial, estamos a escolher a imagem `RedHat:RHEL-HA:7.6:7.6.2019062019` para o exemplo RHEL 7 e a escolher `RedHat:RHEL-HA:8.1:8.1.2020021914` para o exemplo RHEL 8.
    
    Também pode escolher SQL Server 2019 pré-instalado em imagens RHEL8-HA. Para obter a lista destas imagens, executar o seguinte comando:  
    
    ```azurecli-interactive
    az vm image list --all --offer "sql2019-rhel8"
    ```

    Deverá ver os seguintes resultados:

    ```output
    [
      {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200317",
    "version": "15.0.200317"
       },
       }
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200512",
    "version": "15.0.200512"
       }
    ]
    ```

    Se utilizar uma das imagens acima para criar as máquinas virtuais, tem o SQL Server 2019 pré-instalado. Ignore a secção [de instalação sql server e ferramentas mssql,](#install-sql-server-and-mssql-tools) conforme descrito neste artigo.
    
    
    > [!IMPORTANT]
    > Os nomes das máquinas devem ter menos de 15 caracteres para configurar o grupo de disponibilidade. O nome de utilizador não pode conter caracteres maiúsculos e as palavras-passe devem ter mais de 12 caracteres.

1. Queremos criar 3 VMs no conjunto de disponibilidade. Substitua o seguinte no comando abaixo:

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

O comando acima cria os VMs e cria um VNet predefinido para esses VMs. Para obter mais informações sobre as diferentes configurações, consulte o artigo [az vm create.](https://docs.microsoft.com/cli/azure/vm)

Deverá obter resultados semelhantes aos seguintes assim que o comando estiver concluído para cada VM:

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
> A imagem padrão que é criada com o comando acima cria um disco de 32GB de SISTEMA por predefinição. Pode ficar sem espaço com esta instalação predefinida. Pode utilizar o seguinte parâmetro adicionado ao comando acima `az vm create` indicado para criar um disco DE com 128GB como exemplo: `--os-disk-size-gb 128` .
>
> Em seguida, pode [configurar o Gestor de Volume Lógico (LVM)](../../../virtual-machines/linux/configure-lvm.md) se precisar de expandir os volumes de pastas apropriados para acomodar a sua instalação.

### <a name="test-connection-to-the-created-vms"></a>Ligação de teste aos VMs criados

Ligue-se a VM1 ou a outros VMs utilizando o seguinte comando em Azure Cloud Shell. Se não conseguir encontrar os seus IPs VM, siga este [Quickstart na Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Se a ligação for bem sucedida, deverá ver a seguinte saída que representa o terminal Linux:

```output
[<username>@<VM1> ~]$
```

Escreva `exit` para sair da sessão SSH.

## <a name="enable-high-availability"></a>Ativar a alta disponibilidade

> [!IMPORTANT]
> Para completar esta parte do tutorial, você deve ter uma subscrição para RHEL e o Add-on de Alta Disponibilidade. Se estiver a utilizar uma imagem recomendada na secção anterior, não terá de registar outra subscrição.
 
Ligue-se a cada nó VM e siga o guia abaixo para ativar ha. Para obter mais informações, consulte [ativar a subscrição de alta disponibilidade para a RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Será mais fácil se abrir uma sessão de SSH a cada um dos VMs simultaneamente, uma vez que os mesmos comandos terão de ser executados em cada VM ao longo do artigo.
>
> Se estiver a copiar e colar `sudo` vários comandos, e for solicitado uma palavra-passe, os comandos adicionais não serão executados. Executar cada comando separadamente.


1. Executar os seguintes comandos em cada VM para abrir as portas de firewall pacemaker:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Atualizar e instalar pacotes pacemaker em todos os nós utilizando os seguintes comandos:

    > [!NOTE]
    > **o nmap** é instalado como parte deste bloco de comando como uma ferramenta para encontrar endereços IP disponíveis na sua rede. Não é necessário instalar **o NMAP,** mas será útil mais tarde neste tutorial.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Desa estada a palavra-passe para o utilizador predefinido que é criado ao instalar pacotes pacemaker. Use a mesma palavra-passe em todos os nós.

    ```bash
    sudo passwd hacluster
    ```

1. Utilize o seguinte comando para abrir o ficheiro dos anfitriões e configurar a resolução do nome do anfitrião. Para obter mais informações, consulte [a Configure AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) sobre a configuração do ficheiro hostes.

    ```
    sudo vi /etc/hosts
    ```

    No **editor vi,** `i` insira para inserir texto, e numa linha em branco, adicione o **IP Privado** do VM correspondente. Em seguida, adicione o nome VM após um espaço ao lado do IP. Cada linha deve ter uma entrada separada.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Recomendamos que utilize o seu endereço **IP privado** acima. A utilização do endereço IP público nesta configuração fará com que a configuração falhe e não recomendamos a exposição do seu VM a redes externas.

    Para sair do **editor vi,** primeiro bata na chave **Esc,** e depois insira o comando `:wq` para escrever o ficheiro e desistir.

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster Pacemaker

Nesta secção, ativaremos e iniciaremos o serviço pcsd e, em seguida, configuraremos o cluster. Para o SQL Server no Linux, os recursos de cluster não são criados automaticamente. Precisamos de ativar e criar os recursos do pacemaker manualmente. Para obter mais informações, consulte o artigo sobre [a configuração de uma instância de cluster de failover para a RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Ativar e iniciar o serviço pcsd e pacemaker

1. Executar os comandos em todos os nós. Estes comandos permitem que os nós voltem a juntar-se ao cluster após o reboot.

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

1. No nó primário, executar os seguintes comandos para configurar o cluster.

    - Ao executar o `pcs cluster auth` comando para autenticar os nós de cluster, será solicitado uma palavra-passe. Introduza a palavra-passe para o utilizador **hacluster** criado anteriormente.

    **RHEL7**

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

    **RHEL8**

    Para o RHEL 8, terá de autenticar os nós separadamente. Introduza manualmente o nome de utilizador e a palavra-passe para **hacluster** quando solicitado.

    ```bash
    sudo pcs host auth <node1> <node2> <node3>
    sudo pcs cluster setup <clusterName> <node1> <node2> <node3>
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Executar o seguinte comando para verificar se todos os nós estão on-line.

    ```bash
    sudo pcs status
    ```

   **RHEL 7** 
   
    Se todos os nós estiverem online, verá uma saída semelhante à seguinte:

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
   
   **RHEL 8** 
   
    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Cluster Summary:
    * Stack: corosync
    * Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    * Last updated: Fri Aug 23 18:27:57 2019
    * Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
    * 3 nodes configured
    * 0 resource instances configured
     
   Node List:
    * Online: [ <VM1> <VM2> <VM3> ]
   
   Full List of Resources:
   * No resources
     
   Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    
    ```
    
1. Definir votos esperados no cluster ao vivo para 3. Este comando apenas afeta o cluster vivo e não altera os ficheiros de configuração.

    Em todos os nós, dediguia os votos esperados com o seguinte comando:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Configure o agente de esgrima

Um dispositivo STONITH fornece um agente de esgrima. As instruções abaixo são modificadas para este tutorial. Para obter mais informações, consulte [a criação de um dispositivo STONITH](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Verifique a versão do Agente da Cerca Azure para garantir que está atualizada](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Utilize o seguinte comando:

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

### <a name="register-a-new-application-in-azure-active-directory"></a>Registar uma nova aplicação no Azure Ative Directory
 
 1. Ir para https://portal.azure.com
 2. Abra a lâmina do [Diretório Ativo Azure](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Vá ao Properties e escreva o ID do Diretório. Este é o `tenant ID`
 3. Clique nos [ **registos da App**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Clique **em Novo registo**
 5. Insira um **Nome** como `<resourceGroupName>-app` , **selecione Contas neste diretório de organização apenas**
 6. Selecione Application Type **Web**, insira um URL de entrada de inscrição (por http://localhost) exemplo, e clique em Adicionar. O URL de inscrição não é utilizado e pode ser qualquer URL válido. Uma vez feito, clique **em Registar**
 7. Selecione **Certificados e segredos** para o seu novo registo de App e, em seguida, clique em **Novo segredo de cliente**
 8. Introduza uma descrição para uma nova chave (segredo de cliente), **selecione Nunca expire** e clique em **Adicionar**
 9. Anota o valor do segredo. É usado como a palavra-passe para o diretor de serviço
10. Selecione **Descrição geral**. Escreva o ID da aplicação. É usado como nome de utilizador (ID de login nos passos abaixo) do Diretor de Serviço
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Crie um papel personalizado para o agente da cerca

Siga o tutorial para [Criar um papel personalizado Azure usando Azure CLI](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

O seu ficheiro json deve ser semelhante ao seguinte:

- `<username>`Substitua-o por um nome à sua escolha. Isto é para evitar qualquer duplicação ao criar esta definição de papel.
- `<subscriptionId>`Substitua-o pelo ID de subscrição Azure.

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

- `<filename>`Substitua-o pelo nome do ficheiro.
- Se estiver a executar o comando a partir de um caminho diferente da pasta para a qual o ficheiro é guardado, inclua o caminho da pasta do ficheiro no comando.

```bash
az role definition create --role-definition "<filename>.json"
```

Deverá ver o seguinte resultado:

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

### <a name="assign-the-custom-role-to-the-service-principal"></a>Atribuir o papel personalizado ao Diretor de Serviços

Atribua o papel personalizado `Linux Fence Agent Role-<username>` que foi criado no último passo para o Diretor de Serviços. Não utilize mais a função Proprietário!
 
1. Ir para https://portal.azure.com
2. Abra a [lâmina de todos os recursos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Selecione a máquina virtual do primeiro nó de cluster
4. Clique **no controlo de acesso (IAM)**
5. Clique **Em Adicionar uma atribuição de função**
6. Selecione o papel `Linux Fence Agent Role-<username>` da lista **role**
7. Na lista **Select,** insira o nome da aplicação que criou acima, `<resourceGroupName>-app`
8. Clicar em **Guardar**
9. Repita os passos acima para o nó de aglomerado.

### <a name="create-the-stonith-devices"></a>Criar os dispositivos STONITH

Executar os seguintes comandos no nó 1:

- Substitua-o `<ApplicationID>` pelo valor de ID do seu registo de inscrição.
- Substitua-o `<servicePrincipalPassword>` pelo valor do segredo do cliente.
- Substitua-o `<resourceGroupName>` pelo grupo de recursos da sua assinatura utilizada para este tutorial.
- Substitua `<tenantID>` o e o da sua Assinatura `<subscriptionId>` Azure.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Uma vez que já adicionámos uma regra à nossa firewall para permitir o serviço HA `--add-service=high-availability` , não há necessidade de abrir as seguintes portas de firewall em todos os nós: 2224, 3121, 21064, 5405. No entanto, se estiver a experimentar qualquer tipo de problemas de conexão com HA, utilize o seguinte comando para abrir estas portas que estão associadas ao HA.

> [!TIP]
> Pode opcionalmente adicionar todas as portas neste tutorial de uma só vez para economizar algum tempo. As portas que precisam de ser abertas são explicadas nas suas secções relativas abaixo. Se quiser adicionar todas as portas agora, adicione as portas adicionais: 1433 e 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Instale o SQL Server e as ferramentas mssql

> [!NOTE]
> Se criou os VMs com o SQL Server 2019 pré-instalado no RHEL8-HA, então pode saltar os passos abaixo para instalar o SQL Server e as ferramentas mssql e iniciar a secção **Configure um Grupo de Disponibilidade** depois de configurar a palavra-passe sa em todos os VMs, executando o comando em todos os `sudo /opt/mssql/bin/mssql-conf set-sa-password` VMs.

Utilize a secção abaixo para instalar o SQL Server e as ferramentas de mssql nos VMs. Pode escolher uma das amostras abaixo para instalar o SQL Server 2017 no RHEL 7 ou SQL Server 2019 no RHEL 8. Execute cada uma destas ações em todos os nós. Para obter mais informações, consulte [instalar o Servidor SQL num VM de chapéu vermelho](/sql/linux/quickstart-install-connect-red-hat).


### <a name="installing-sql-server-on-the-vms"></a>Instalação do SqL Server nos VMs

São utilizados os seguintes comandos para instalar o SQL Server:

**RHEL 7 com SQL Server 2017** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

**RHEL 8 com SQL Server 2019** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```
### <a name="open-firewall-port-1433-for-remote-connections"></a>Abra a porta de firewall 1433 para ligações remotas

Terá de abrir a porta 1433 na VM para se ligar remotamente. Utilize os seguintes comandos para abrir a porta 1433 na firewall de cada VM:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Instalação de ferramentas de linha de comando do SQL Server

Os seguintes comandos são utilizados para instalar ferramentas de linha de comando sql Server. Para obter mais informações, consulte [instalar as ferramentas da linha de comando do SQL Server](/sql/linux/quickstart-install-connect-red-hat#tools).

**RHEL 7** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```

**RHEL 8** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/8/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Para conveniência, adicione /opt/mssql-tools/bin/ à variável ambiente PATH. Isto permite-lhe executar as ferramentas sem especificar o caminho completo. Execute os comandos seguintes para modificar PATH para sessões de início de sessão e sessões interativas/sem início de sessão:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Verifique o estado do Servidor SQL

Uma vez terminada a configuração, pode verificar o estado do SQL Server e verificar se está a funcionar:

```bash
systemctl status mssql-server --no-pager
```

Deverá ver o seguinte resultado:

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

## <a name="configure-an-availability-group"></a>Configure um grupo de disponibilidade

Utilize os seguintes passos para configurar um sql servidor sempre no grupo de disponibilidade para os seus VMs. Para obter mais informações, consulte o [Servidor SQL Configurar sempre em grupos de disponibilidade para uma elevada disponibilidade no Linux](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-always-on-availability-groups-and-restart-mssql-server"></a>Ativar sempre em grupos de disponibilidade e reiniciar o mssql-servidor

Ativar sempre em grupos de disponibilidade em cada nó que hospeda uma instância SQL Server. Em seguida, reinicie o mssql-servidor. Execute o seguintes script:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Criar um certificado

Atualmente, não apoiamos a autenticação da AD no ponto final da AG. Portanto, temos de usar um certificado para encriptação de ponto final AG.

1. Ligue-se a **todos os nós** utilizando o SQL Server Management Studio (SSMS) ou o SQL CMD. Executar os seguintes comandos para permitir uma sessão de AlwaysOn_health e criar uma chave mestra:

    > [!IMPORTANT]
    > Se estiver a ligar remotamente à sua instância SQL Server, terá de ter a porta 1433 aberta na sua firewall. Também terá de permitir ligações de entrada à porta 1433 no seu NSG para cada VM. Para obter mais informações, consulte [Criar uma regra de segurança](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) para criar uma regra de segurança de entrada.

    - Substitua-a `<Master_Key_Password>` por uma palavra-passe própria.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Ligue-se à réplica primária utilizando SSMS ou SQL CMD. Os comandos abaixo criarão um certificado `/var/opt/mssql/data/dbm_certificate.cer` e uma chave privada na réplica principal do `var/opt/mssql/data/dbm_certificate.pvk` SQL Server:

    - Substitua-a `<Private_Key_Password>` por uma palavra-passe própria.
    
    ```sql
    CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
    GO
    
    BACKUP CERTIFICATE dbm_certificate
       TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
       WITH PRIVATE KEY (
               FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
               ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
           );
    GO
    ```

Saia da sessão CMD SQL executando o `exit` comando e volte à sua sessão de SSH.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Copie o certificado para as réplicas secundárias e crie os certificados no servidor

1. Copie os dois ficheiros que foram criados para a mesma localização em todos os servidores que irão acolher réplicas de disponibilidade.
 
    No servidor primário, executar o seguinte `scp` comando para copiar o certificado para os servidores-alvo:

    - Substitua `<username>` e pelo nome de utilizador e pelo nome `<VM2>` VM-alvo que está a utilizar.
    - Executar este comando para todas as réplicas secundárias.

    > [!NOTE]
    > Não tens de `sudo -i` correr, o que te dá o ambiente de raiz. Podias executar o `sudo` comando em frente a cada comando, como fizemos anteriormente neste tutorial.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. No servidor alvo, executar o seguinte comando:

    - Substitua `<username>` pelo seu nome de utilizador.
    - O `mv` comando move os ficheiros ou diretório de um lugar para outro.
    - O `chown` comando é utilizado para alterar o proprietário e o grupo de ficheiros, diretórios ou links.
    - Executar estes comandos para todas as réplicas secundárias.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. O seguinte script Transact-SQL cria um certificado a partir da cópia de segurança que criou na réplica principal do SQL Server. Atualize o script com senhas fortes. A palavra-passe de desencriptação é a mesma palavra-passe que usou para criar o ficheiro .pvk no passo anterior. Para criar o certificado, execute o seguinte script utilizando CMD ou SSMS SQL em todos os servidores secundários:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Criar pontos finais espelhadores de base de dados em todas as réplicas

Execute o seguinte script em todas as instâncias do SQL Server utilizando CMD ou SSMS SQL:

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
GO
```

### <a name="create-the-availability-group"></a>Criar o grupo de disponibilidade

Ligue-se à instância sql Server que acolhe a réplica primária utilizando CMD ou SSMS SQL. Executar o seguinte comando para criar o grupo de disponibilidade:

- `ag1`Substitua-o pelo nome do grupo disponibilidade pretendido.
- Substitua o `<VM1>` `<VM2>` , e `<VM3>` valores com os nomes das instâncias sql Server que acolhem as réplicas.

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
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Crie um login do SQL Server para Pacemaker

Em todas as instâncias do SQL Server, crie um login DO SQL Server para Pacemaker. O seguinte Transact-SQL cria um login.

- `<password>`Substitua-a pela sua própria senha complexa.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

Em todas as instâncias do SQL Server, guarde as credenciais utilizadas para o login do SQL Server. 

1. Criar o ficheiro:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Adicione as seguintes 2 linhas ao ficheiro:

    ```bash
    pacemakerLogin
    <password>
    ```

    Para sair do **editor vi,** primeiro bata na chave **Esc,** e depois insira o comando `:wq` para escrever o ficheiro e desistir.

1. Tornar o ficheiro apenas legível por raiz:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Junte réplicas secundárias ao grupo de disponibilidade

1. Para se juntar às réplicas secundárias à AG, terá de abrir a porta 5022 na firewall para todos os servidores. Executar o seguinte comando na sua sessão SSH:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. Nas suas réplicas secundárias, execute os seguintes comandos para se juntar a eles na AG:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Executar o seguinte script Transact-SQL na réplica primária e cada réplica secundária:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. Uma vez que as réplicas secundárias são unidas, você pode vê-las no SSMS Object Explorer expandindo o nó **Always On High Availability:**

    ![A screenshot mostra as réplicas de disponibilidade primária e secundária.](./media/rhel-high-availability-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Adicione uma base de dados ao grupo de disponibilidade

Seguiremos o artigo do [grupo de configuração sobre a adição de uma base de dados.](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group)

Neste passo são utilizados os seguintes comandos Transact-SQL. Executar estes comandos na réplica primária:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Verifique se a base de dados é criada nos servidores secundários

Em cada réplica secundária do SQL Server, execute a seguinte consulta para ver se a base de dados db1 foi criada e está em estado SINCRONIZADO:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Se as `synchronization_state_desc` listas `db1` sincronizadas, isto significa que as réplicas estão sincronizadas. Os secundários estão a aparecer `db1` na réplica primária.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Criar recursos de grupo de disponibilidade no cluster Pacemaker

Vamos seguir o guia para [criar os recursos do grupo de disponibilidade no cluster Pacemaker.](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)

### <a name="create-the-ag-cluster-resource"></a>Criar o recurso cluster AG

1. Utilize um dos seguintes comandos com base no ambiente escolhido anteriormente para criar o recurso `ag_cluster` no grupo de disponibilidade `ag1` .

    **RHEL 7**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

    **RHEL 8**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s promotable notify=true
    ```

2. Verifique o seu recurso e certifique-se de que estão online antes de prosseguir usando o seguinte comando:

    ```bash
    sudo pcs resource
    ```

    Deverá ver o seguinte resultado:
    
    **RHEL 7** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```
    
    **RHEL 8** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    * Clone Set: ag_cluster-clone [ag_cluster] (promotable):
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel3 (Monitoring) 
    * ag_cluster             (ocf::mssql:ag) :            Master VMrhel1 (Monitoring)
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel2 (Monitoring)
    ```


### <a name="create-a-virtual-ip-resource"></a>Criar um recurso IP virtual

1. Utilize um endereço IP estático disponível a partir da sua rede para criar um recurso IP virtual. Pode encontrar um utilizando a ferramenta de comando `nmap` .

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

2. Desficer a propriedade **ativada por stonith** para falso

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

3. Crie o recurso IP virtual utilizando o seguinte comando:

    - Substitua o `<availableIP>` valor abaixo por um endereço IP não uused.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Adicionar restrições

1. Para garantir que o endereço IP e o recurso AG estão a funcionar no mesmo nó, deve ser configurada uma restrição de colocação. Execute o seguinte comando:

   **RHEL 7**
  
    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

   **RHEL 8**
   
    ```bash
     sudo pcs constraint colocation add virtualip with master ag_cluster-clone INFINITY with-rsc-role=Master
    ```
  
2. Crie uma restrição de encomenda para garantir que o recurso AG está a funcionar antes do endereço IP. Embora o constrangimento de colocação implique uma restrição de encomenda, isto o impõe.

   **RHEL 7**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-clone then start virtualip
    ```
  
3. Para verificar os constrangimentos, executar o seguinte comando:

    ```bash
    sudo pcs constraint list --full
    ```

    Deverá ver o seguinte resultado:
    
    **RHEL 7**

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```
    
    **RHEL 8**
    
    ```output
    Location Constraints:
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Re-ativar a stonith

Estamos prontos para os testes. Re-activar a stonith no cluster executando o seguinte comando no nó 1:

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

Para garantir que a configuração tenha sido bem sucedida até agora, vamos testar uma falha. Para obter mais informações, consulte [Always On availability group failover no Linux](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Executar o seguinte comando para falhar manualmente sobre a réplica primária para `<VM2>` . `<VM2>`Substitua-o pelo valor do nome do seu servidor.

   **RHEL 7**
   
    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master
    ```

2. Se verificar novamente os seus constrangimentos, verá que foi adicionado outro constrangimento devido à falha manual:
    
    **RHEL 7**
    
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

    **RHEL 8**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-clone)
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```
    
3. Remover a restrição com ID `cli-prefer-ag_cluster-master` utilizando o seguinte comando:

    **RHEL 7**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

    **RHEL 8**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-clone
    ```

1. Verifique os recursos do seu cluster utilizando o `sudo pcs resource` comando, e deve ver se a instância principal é agora `<VM2>` .

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

## <a name="test-fencing"></a>Esgrima de teste

Pode testar o STONITH executando o seguinte comando. Tente executar o comando abaixo de `<VM1>` `<VM3>` .

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Por defeito, a ação da vedação desliga o nó e depois continua. Se quiser apenas desligar o nó, utilize a opção `--off` no comando.

Deverá obter o seguinte resultado:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Para obter mais informações sobre o teste de um dispositivo de cerca, consulte o seguinte artigo [do Red Hat.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar)

## <a name="next-steps"></a>Passos seguintes

Para utilizar um ouvinte de grupo de disponibilidade para as suas instâncias DO SQL Server, terá de criar e configurar um equilibrador de carga.

> [!div class="nextstepaction"]
> [Tutorial: Configurar um ouvinte de grupo de disponibilidade para o SQL Server em máquinas virtuais RHEL em Azure](rhel-high-availability-listener-tutorial.md)
