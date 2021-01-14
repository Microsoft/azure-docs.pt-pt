---
title: Criar e gerir um Windows VM em Azure usando Python
description: Aprenda a usar python para criar e gerir um Windows VM em Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 06/22/2017
ms.author: cynthn
ms.custom: devx-track-python
ms.openlocfilehash: 15714405c683889c0f783359011a0f0f5f9f3f43
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201795"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Criar e gerir VMs windows em Azure usando Python

[Uma Máquina Virtual Azure](overview.md) (VM) precisa de vários recursos de suporte Azure. Este artigo abrange a criação, gestão e eliminação de recursos VM usando Python. Saiba como:

> [!div class="checklist"]
> * Criar um projeto do Visual Studio
> * Instalar pacotes
> * Criar credenciais
> * Criar recursos
> * Executar tarefas de gestão
> * Eliminar recursos
> * Executar a aplicação

Leva cerca de 20 minutos para fazer estes passos.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Se ainda não o fez, instale [o Visual Studio](/visualstudio/install/install-visual-studio). Selecione **o desenvolvimento python** na página Workloads e, em seguida, clique em **Instalar**. No resumo, pode ver que **python 3 64-bit (3.6.0)** é automaticamente selecionado para si. Se já instalou o Visual Studio, pode adicionar a carga de trabalho python utilizando o Lançador de Estúdio Visual.
2. Depois de instalar e iniciar o Visual Studio, clique em **File**  >  **New**  >  **Project**.
3. Clique **em Modelos**  >  **Python**  >  **Python Application,** insira *myPythonProject* para o nome do projeto, selecione a localização do projeto e, em seguida, clique **em OK**.

## <a name="install-packages"></a>Instalar pacotes

1. No Solution Explorer, sob *o myPythonProject,* clique à direita **em Python Environments** e, em seguida, selecione **Adicionar ambiente virtual**.
2. No ecrã Add Virtual Environment, aceite o nome padrão do *env,* certifique-se de que *Python 3.6 (64-bits)* é selecionado para o intérprete base e, em seguida, clique em **Criar**.
3. Clique com o botão direito no ambiente *env* que criou, clique em **Instalar o Pacote Python,** *introduza* o azul na caixa de pesquisa e, em seguida, prima Enter.

Deve ver nas janelas de saída que os pacotes azul foram instalados com sucesso. 

## <a name="create-credentials"></a>Criar credenciais

Antes de iniciar este passo, certifique-se de que tem um [diretor de serviço de Diretório Ativo.](../../active-directory/develop/howto-create-service-principal-portal.md) Você também deve registar o ID da aplicação, a chave de autenticação, e o ID do inquilino que você precisa em um passo posterior.

1. Abra *myPythonProject.py* ficheiro que foi criado e, em seguida, adicione este código para permitir que a sua aplicação seja executada:

    ```python
    if __name__ == "__main__":
    ```

2. Para importar o código necessário, adicione estas declarações ao topo do ficheiro .py:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Em seguida, no ficheiro .py, adicione variáveis após as declarações de importação para especificar os valores comuns utilizados no código:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Substitua o **id de subscrição** pelo seu identificador de assinatura.

4. Para criar as credenciais do Ative Directory que precisa de fazer pedidos, adicione esta função após as variáveis no ficheiro .py:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Substitua **o id de aplicação,** **a chave de autenticação** e o **id do inquilino** pelos valores que recolheu anteriormente quando criou o seu diretor de serviço Azure Ative.

5. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Criar recursos
 
### <a name="initialize-management-clients"></a>Inicializar clientes de gestão

Os clientes de gestão são necessários para criar e gerir recursos usando o Python SDK em Azure. Para criar os clientes de gestão, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

```python
resource_group_client = ResourceManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>Criar o VM e apoiar recursos

Todos os recursos devem ser contidos num [grupo de recursos.](../../azure-resource-manager/management/overview.md)

1. Para criar um grupo de recursos, adicione esta função após as variáveis no ficheiro .py:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Os conjuntos de disponibilidade](tutorial-availability-sets.md) facilitam a manutenção das máquinas virtuais utilizadas pela sua aplicação.

1. Para criar um conjunto de disponibilidade, adicione esta função após as variáveis no ficheiro .py:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

É necessário um [endereço IP público](../../virtual-network/public-ip-addresses.md) para comunicar com a máquina virtual.

1. Para criar um endereço IP público para a máquina virtual, adicione esta função após as variáveis no ficheiro .py:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Uma máquina virtual deve estar numa sub-rede de uma [rede Virtual.](../../virtual-network/virtual-networks-overview.md)

1. Para criar uma rede virtual, adicione esta função após as variáveis no ficheiro .py:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Para adicionar uma sub-rede à rede virtual, adicione esta função após as variáveis no ficheiro .py:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Uma máquina virtual precisa de uma interface de rede para comunicar na rede virtual.

1. Para criar uma interface de rede, adicione esta função após as variáveis no ficheiro .py:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Agora que criou todos os recursos de apoio, pode criar uma máquina virtual.

1. Para criar a máquina virtual, adicione esta função após as variáveis no ficheiro .py:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > Este tutorial cria uma máquina virtual que executa uma versão do sistema operativo Windows Server. Para saber mais sobre a seleção de outras imagens, consulte [a Navigate e selecione imagens de máquinas virtuais Azure com o Windows PowerShell e o Azure CLI](../linux/cli-ps-findimage.md).
    > 
    > 

2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Executar tarefas de gestão

Durante o ciclo de vida de uma máquina virtual, poderá querer executar tarefas de gestão, como iniciar, parar ou eliminar uma máquina virtual. Além disso, pode querer criar código para automatizar tarefas repetitivas ou complexas.

### <a name="get-information-about-the-vm"></a>Obtenha informações sobre o VM

1. Para obter informações sobre a máquina virtual, adicione esta função após as variáveis no ficheiro .py:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Parar a VM

Pode parar uma máquina virtual e manter todas as suas definições, mas continuar a ser carregada por ela, ou pode parar uma máquina virtual e translocar a máquina. Quando uma máquina virtual é negociada, todos os recursos associados a ela também são translocados e a faturação termina para ela.

1. Para parar a máquina virtual sem a fazer negóciolocado, adicione esta função após as variáveis no ficheiro .py:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Se quiser negociar a máquina virtual, altere a power_off ligue para este código:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Iniciar a VM

1. Para iniciar a máquina virtual, adicione esta função após as variáveis no ficheiro .py:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Redimensionar o VM

Muitos aspetos da implementação devem ser considerados ao decidir sobre um tamanho para a sua máquina virtual. Para mais informações, consulte os [tamanhos de VM.](../sizes.md)

1. Para alterar o tamanho da máquina virtual, adicione esta função após as variáveis no ficheiro .py:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Adicionar um disco de dados à VM

As máquinas virtuais podem ter um ou mais [discos de dados](../managed-disks-overview.md) que são armazenados como VHDs.

1. Para adicionar um disco de dados à máquina virtual, adicione esta função após as variáveis no ficheiro .py: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Eliminar recursos

Como é cobrado pelos recursos utilizados no Azure, é sempre uma boa prática apagar recursos que já não são necessários. Se pretender eliminar as máquinas virtuais e todos os recursos de suporte, tudo o que tem de fazer é eliminar o grupo de recursos.

1. Para eliminar o grupo de recursos e todos os recursos, adicione esta função após as variáveis no ficheiro .py:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Para ligar para a função que adicionou anteriormente, adicione este código ao abrigo da declaração **se** a declaração no final do ficheiro .py:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Salve *myPythonProject.py.*

## <a name="run-the-application"></a>Executar a aplicação

1. Para executar a aplicação da consola, clique em **Iniciar** no Estúdio Visual.

2. Prima **Introduza** após a reposição do estado de cada recurso. Na informação sobre o estado, deve ver um estado de provisionamento **bem sucedido.** Depois de a máquina virtual ser criada, tem a oportunidade de apagar todos os recursos que cria. Antes de pressionar **Enter** para começar a apagar recursos, pode demorar alguns minutos a verificar a sua criação no portal Azure. Se tiver o portal Azure aberto, poderá ter de refrescar a lâmina para ver novos recursos.  

    Deve levar cerca de cinco minutos para que esta aplicação da consola corra completamente do início ao fim. Pode demorar vários minutos após o fim da aplicação antes de todos os recursos e o grupo de recursos serem eliminados.


## <a name="next-steps"></a>Passos seguintes

- Se ocorreram problemas com a implementação, um passo seguinte será ver [Troubleshooting resource group deployments with Azure portal (Resolução de problemas com implementações do grupo de recursos com o portal do Azure)](../../azure-resource-manager/templates/deployment-history.md)
- Saiba mais sobre a [Biblioteca Azure Python](/python/api/overview/azure/?view=azure-python)