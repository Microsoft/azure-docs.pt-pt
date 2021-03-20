---
title: Utilizar a API de Gestão de Serviços (Python) - guia de recursos
description: Aprenda a realizar programáticamente tarefas comuns de gestão de serviços a partir de Python.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 02993f2b79e37e5e50c20c4ee07220bcbd36edb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741405"
---
# <a name="use-service-management-from-python"></a>Utilize a gestão de serviços da Python

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Este guia mostra-lhe como executar programáticamente tarefas comuns de gestão de serviços a partir de Python. A classe **ServiceManagementService** no [Azure SDK para Python](https://github.com/Azure/azure-sdk-for-python) suporta o acesso programático a grande parte da funcionalidade relacionada com a gestão de serviços que está disponível no [portal Azure.][management-portal] Pode utilizar esta funcionalidade para criar, atualizar e eliminar serviços na nuvem, implementações, serviços de gestão de dados e máquinas virtuais. Esta funcionalidade pode ser útil na construção de aplicações que necessitem de acesso programático à gestão de serviços.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>O que é a gestão de serviços?
A Azure Service Management API proporciona acesso programático a grande parte da funcionalidade de gestão de serviços disponível através do [portal Azure.][management-portal] Você pode usar o Azure SDK para Python para gerir os seus serviços em nuvem e contas de armazenamento.

Para utilizar a API de Gestão de Serviços, é necessário [criar uma conta Azure.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="concepts"></a><a name="Concepts"> </a>Conceitos
O Azure SDK for Python envolve a [API de Gestão de Serviços,][svc-mgmt-rest-api]que é uma API REST. Todas as operações da API são realizadas através de TLS e mutuamente autenticadas utilizando certificados X.509 v3. O serviço de gestão pode ser acedido a partir de um serviço em execução em Azure. Também pode ser acedido diretamente pela Internet a partir de qualquer aplicação que possa enviar um pedido HTTPS e receber uma resposta HTTPS.

## <a name="installation"></a><a name="Installation"> </a>Instalação
Todas as funcionalidades descritas neste artigo estão disponíveis na `azure-servicemanagement-legacy` embalagem, que pode instalar utilizando pip. Para obter mais informações sobre a instalação (por exemplo, se for novo em Python), consulte [Install Python e o Azure SDK](/azure/developer/python/azure-sdk-install).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Ligar à gestão de serviços
Para se ligar ao ponto final de gestão de serviços, precisa do seu ID de subscrição Azure e de um certificado de gestão válido. Pode obter o seu ID de subscrição através do [portal Azure.][management-portal]

> [!NOTE]
> Agora pode utilizar certificados criados com OpenSSL quando estiver em execução no Windows. Python 2.7.4 ou mais tarde é necessário. Recomendamos que utilize o OpenSSL em vez de .pfx, porque o suporte para certificados .pfx é suscetível de ser removido no futuro.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificados de gestão no Windows/Mac/Linux (OpenSSL)
Pode utilizar [o OpenSSL](https://www.openssl.org/) para criar o seu certificado de gestão. É necessário criar dois certificados, um para o servidor (um `.cer` ficheiro) e outro para o cliente (um `.pem` ficheiro). Para criar o `.pem` ficheiro, execute:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
```

Para criar o `.cer` certificado, execute:

```console
openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer
```

Para obter mais informações sobre os certificados Azure, consulte [a visão geral dos Certificados para os Serviços Azure Cloud](cloud-services-certs-create.md). Para obter uma descrição completa dos parâmetros OpenSSL, consulte a documentação em [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html) .

Depois de criar estes ficheiros, faça o upload do `.cer` ficheiro para o Azure. No [portal Azure][management-portal], no separador **Definições,** selecione **Upload**. Note onde guardou o `.pem` ficheiro.

Depois de obter o seu ID de subscrição, crie um certificado e faça o upload do `.cer` ficheiro para a Azure, conecte-se ao ponto final de gestão do Azure. Conecte-se passando o ID de subscrição e o caminho para o `.pem` ficheiro para **ServiceManagementService**.

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = '<path_to_.pem_certificate>'

sms = ServiceManagementService(subscription_id, certificate_path)
```

No exemplo anterior, `sms` encontra-se um objeto **ServiceManagementService.** A classe **ServiceManagementService** é a classe primária usada para gerir os serviços Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificados de gestão no Windows (MakeCert)
Pode criar um certificado de gestão auto-assinado na sua máquina utilizando `makecert.exe` . Abra um **pedido de comando do Estúdio Visual** como **administrador** e utilize o seguinte comando, substituindo *O AzureCertificate* pelo nome de certificado que pretende utilizar:

```console
makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"
```

O comando cria o `.cer` ficheiro e instala-o na loja de certificados **Pessoal.** Para mais informações, consulte [a visão geral dos Certificados para serviços em nuvem Azure.](cloud-services-certs-create.md)

Depois de criar o certificado, faça o upload do `.cer` ficheiro para a Azure. No [portal Azure][management-portal], no separador **Definições,** selecione **Upload**.

Depois de obter o seu ID de subscrição, crie um certificado e faça o upload do `.cer` ficheiro para a Azure, conecte-se ao ponto final de gestão do Azure. Conecte-se passando o ID de subscrição e a localização do certificado na sua loja de **certificados Pessoal** ao **ServiceManagementService** (mais uma vez, substitua *o AzureCertificate* pelo nome do seu certificado).

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

sms = ServiceManagementService(subscription_id, certificate_path)
```

No exemplo anterior, `sms` encontra-se um objeto **ServiceManagementService.** A classe **ServiceManagementService** é a classe primária usada para gerir os serviços Azure.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Lista de locais disponíveis
Para listar as localizações disponíveis para os serviços de hospedagem, utilize o método **\_ de locais da lista.**

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_locations()
for location in result:
    print(location.name)
```

Quando cria um serviço de cloud ou serviço de armazenamento, precisa fornecer uma localização válida. O método **\_ de localizações** da lista devolve sempre uma lista atualizada das localizações atualmente disponíveis. A partir desta escrita, os locais disponíveis são:

* Europa Ocidental
* Europa do Norte
* Sudeste Asiático
* Ásia Leste
* E.U.A. Central
* E.U.A. Centro-Norte
* E.U.A. Centro-Sul
* E.U.A. Oeste
* E.U.A. Leste
* Leste do Japão
* Oeste do Japão
* Sul do Brasil
* Leste da Austrália
* Austrália Sudeste

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Criar um serviço de nuvem
Quando cria uma aplicação e a executa em Azure, o código e a configuração em conjunto são chamados de [serviço de nuvem][cloud service]Azure . (Era conhecido como um *serviço hospedado* em lançamentos anteriores do Azure.) Pode utilizar o método **\_ de \_ serviço criado** para criar um novo serviço hospedado. Crie o serviço fornecendo um nome de serviço hospedado (que deve ser único em Azure), uma etiqueta (automaticamente codificada para base64), uma descrição e uma localização.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myhostedservice'
label = 'myhostedservice'
desc = 'my hosted service'
location = 'West US'

sms.create_hosted_service(name, label, desc, location)
```

Pode listar todos os serviços hospedados para a sua subscrição com o método de **\_ \_ serviços hospedados** na lista.

```python
result = sms.list_hosted_services()

for hosted_service in result:
    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)
    print('')
```

Para obter informações sobre um determinado serviço hospedado, passe o nome de serviço hospedado para o método **de propriedades de \_ \_ serviço \_ hospedado.**

```python
hosted_service = sms.get_hosted_service_properties('myhostedservice')

print('Service name: ' + hosted_service.service_name)
print('Management URL: ' + hosted_service.url)
print('Location: ' + hosted_service.hosted_service_properties.location)
```

Depois de criar um serviço de cloud, desloque o seu código para o serviço com o método **\_ de implementação de criar.**

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Excluir um serviço de nuvem
Pode eliminar um serviço de cloud passando o nome de serviço para o método **\_ de \_ serviço de excluir** hospedado.

```python
sms.delete_hosted_service('myhostedservice')
```

Antes de poder apagar um serviço, todas as implementações do serviço devem ser eliminadas primeiro. Para obter mais informações, consulte [Eliminar uma implementação](#DeleteDeployment).

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Eliminar uma implantação
Para eliminar uma implementação, utilize o método **de implantação de \_ eliminação.** O exemplo a seguir mostra como eliminar uma implantação denominada `v1` :

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment('myhostedservice', 'v1')
```

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Criar um serviço de armazenamento
Um [serviço de armazenamento](../storage/common/storage-account-create.md) dá-lhe acesso a [blobs](../storage/blobs/storage-quickstart-blobs-python.md)Azure, [mesas](../cosmos-db/table-storage-how-to-use-python.md)e [filas.](../storage/queues/storage-python-how-to-use-queue-storage.md) Para criar um serviço de armazenamento, precisa de um nome para o serviço (entre 3 e 24 caracteres minúsculos e único dentro do Azure). Também precisa de uma descrição, uma etiqueta (até 100 caracteres, codificada automaticamente para base64) e uma localização. O exemplo a seguir mostra como criar um serviço de armazenamento especificando uma localização:

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mystorageaccount'
label = 'mystorageaccount'
location = 'West US'
desc = 'My storage account description.'

result = sms.create_storage_account(name, desc, label, location=location)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

No exemplo anterior, o estado da operação da **\_ \_ conta de armazenamento** criar pode ser recuperado através da passagem do resultado devolvido por criar uma **\_ \_ conta de armazenamento** para o método de **\_ \_ estado de funcionamento.** 

Pode listar as suas contas de armazenamento e as suas propriedades com o método **\_ das \_ contas de armazenamento** da lista.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_storage_accounts()
for account in result:
    print('Service name: ' + account.service_name)
    print('Location: ' + account.storage_service_properties.location)
    print('')
```

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Apagar um serviço de armazenamento
Para eliminar um serviço de armazenamento, passe o nome do serviço de armazenamento para o método **\_ da \_ conta de armazenamento de eliminação.** A eliminação de um serviço de armazenamento elimina todos os dados armazenados no serviço (bolhas, mesas e filas).

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_storage_account('mystorageaccount')
```

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Listar sistemas operativos disponíveis
Para listar os sistemas operativos disponíveis para os serviços de hospedagem, utilize o método **\_ dos \_ sistemas operativos da lista.**

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_operating_systems()

for os in result:
    print('OS: ' + os.label)
    print('Family: ' + os.family_label)
    print('Active: ' + str(os.is_active))
```

Em alternativa, pode utilizar o método das famílias do **sistema operativo da lista, \_ \_ \_** que agru como agrum os sistemas operativos por família.

```python
result = sms.list_operating_system_families()

for family in result:
    print('Family: ' + family.label)
    for os in family.operating_systems:
        if os.is_active:
            print('OS: ' + os.label)
            print('Version: ' + os.version)
    print('')
```

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Criar uma imagem do sistema operativo
Para adicionar uma imagem do sistema operativo ao repositório de imagem, utilize o método **de \_ \_ imagem add os.**

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mycentos'
label = 'mycentos'
os = 'Linux' # Linux or Windows
media_link = 'url_to_storage_blob_for_source_image_vhd'

result = sms.add_os_image(label, media_link, name, os)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

Para listar as imagens do sistema operativo que estão disponíveis, utilize o método **\_ de lista de \_ imagens.** Inclui todas as imagens da plataforma e imagens do utilizador.

```python
result = sms.list_os_images()

for image in result:
    print('Name: ' + image.name)
    print('Label: ' + image.label)
    print('OS: ' + image.os)
    print('Category: ' + image.category)
    print('Description: ' + image.description)
    print('Location: ' + image.location)
    print('Media link: ' + image.media_link)
    print('')
```

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Eliminar uma imagem do sistema operativo
Para eliminar uma imagem do utilizador, utilize o método **de imagem de eliminar \_ os. \_**

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.delete_os_image('mycentos')

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Criar uma máquina virtual
Para criar uma máquina virtual, primeiro precisa de criar um [serviço de cloud](#CreateCloudService). Em seguida, crie a colocação da máquina virtual utilizando o método **\_ de \_ \_ implantação da máquina virtual.**

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

# Name of an os image as returned by list_os_images
image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

# Destination storage account container/blob where the VM disk
# will be created
media_link = 'url_to_target_storage_blob_for_vm_hd'

# Linux VM configuration, you can use WindowsConfigurationSet
# for a Windows VM instead
linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

os_hd = OSVirtualHardDisk(image_name, media_link)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=os_hd,
    role_size='Small')
```

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Eliminar uma máquina virtual
Para eliminar uma máquina virtual, primeiro elimina a implementação utilizando o método **de implantação de \_ eliminação.**

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment(service_name='myvm',
    deployment_name='myvm')
```

O serviço na nuvem pode então ser eliminado utilizando o método **\_ de \_ serviço de eliminação hospedado.**

```python
sms.delete_hosted_service(service_name='myvm')
```

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Crie uma máquina virtual a partir de uma imagem de máquina virtual capturada
Para capturar uma imagem VM, ligue primeiro para o método de **\_ \_ imagem vm de captura.**

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

# replace the below three parameters with actual values
hosted_service_name = 'hs1'
deployment_name = 'dep1'
vm_name = 'vm1'

image_name = vm_name + 'image'
image = CaptureRoleAsVMImage    ('Specialized',
    image_name,
    image_name + 'label',
    image_name + 'description',
    'english',
    'mygroup')

result = sms.capture_vm_image(
        hosted_service_name,
        deployment_name,
        vm_name,
        image
    )
```

Para se certificar de que capturou com sucesso a imagem, utilize a **lista \_ de \_ imagens VM** API. Certifique-se de que a sua imagem está apresentada nos resultados.

```python
images = sms.list_vm_images()
```

Para finalmente criar a máquina virtual utilizando a imagem capturada, utilize o método **\_ de \_ \_ implementação da máquina virtual** como antes, mas este tempo passa no vm_image_name em vez disso.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=None,
    role_size='Small',
    vm_image_name = image_name)
```

Para saber mais sobre como capturar uma máquina virtual Linux no modelo clássico de implementação, consulte [Capture a Linux virtual machine](/previous-versions/azure/virtual-machines/linux/classic/capture-image-classic).

Para saber mais sobre como capturar uma máquina virtual do Windows no modelo clássico de implementação, consulte [Capture a windows virtual machine](/previous-versions/azure/virtual-machines/windows/classic/capture-image-classic).

## <a name="next-steps"></a><a name="What's Next"> </a>Passos seguintes
Agora que aprendeu o básico da gestão de serviços, pode aceder à documentação de referência completa da [API para o Azure Python SDK](https://azure-sdk-for-python.readthedocs.org/) e executar tarefas complexas facilmente para gerir a sua aplicação Python.

Para obter mais informações, consulte o [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: /previous-versions/azure/ee460799(v=azure.100)


[cloud service]:/azure/cloud-services/