---
title: Utilize a API de Gestão de Serviços (Python) - guia de funcionalidades
description: Aprenda a executar programáticamente tarefas comuns de gestão de serviços da Python.
services: cloud-services
documentationcenter: python
author: tanmaygore
manager: vashan
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: tagore
ms.openlocfilehash: 135dd92f7af4397f2053ea0bdc15d98dfad93914
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253361"
---
# <a name="use-service-management-from-python"></a>Use a gestão de serviços da Python
Este guia mostra-lhe como executar programáticamente tarefas comuns de gestão de serviços da Python. A classe **ServiceManagementService** no [Azure SDK para Python](https://github.com/Azure/azure-sdk-for-python) suporta o acesso programático a grande parte da funcionalidade relacionada com a gestão de serviços que está disponível no [portal Azure.][management-portal] Pode utilizar esta funcionalidade para criar, atualizar e eliminar serviços na nuvem, implementações, serviços de gestão de dados e máquinas virtuais. Esta funcionalidade pode ser útil na construção de aplicações que necessitem de acesso programático à gestão de serviços.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>O que é a gestão de serviços?
A API de Gestão de Serviços Azure proporciona acesso programático a grande parte da funcionalidade de gestão de serviços disponível através do [portal Azure.][management-portal] Você pode usar o Azure SDK para Python para gerir seus serviços na nuvem e contas de armazenamento.

Para utilizar a API de Gestão de Serviços, é necessário [criar uma conta Azure.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="concepts"></a><a name="Concepts"> </a>Conceitos
O Azure SDK para Python envolve a API de [Gestão][svc-mgmt-rest-api]de Serviços, que é uma API REST. Todas as operações da API são realizadas através de TLS e autenticadas mutuamente utilizando certificados V3 X.509. O serviço de gestão pode ser acedido a partir de um serviço em funcionamento em Azure. Também pode ser acedido diretamente através da Internet a partir de qualquer aplicação que possa enviar um pedido HTTPS e receber uma resposta HTTPS.

## <a name="installation"></a><a name="Installation"> </a>Instalação
Todas as funcionalidades descritas neste `azure-servicemanagement-legacy` artigo estão disponíveis no pacote, que pode instalar utilizando pip. Para obter mais informações sobre a instalação (por exemplo, se for novo em Python), consulte [Instalar Python e o Azure SDK](/azure/developer/python/azure-sdk-install).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Ligar à gestão de serviços
Para se ligar ao ponto final da gestão do serviço, precisa do seu ID de subscrição Azure e de um certificado de gestão válido. Pode obter o seu ID de subscrição através do [portal Azure][management-portal].

> [!NOTE]
> Agora pode utilizar certificados criados com OpenSSL quando estiver a funcionar no Windows. Python 2.7.4 ou mais tarde é necessário. Recomendamos que utilize o OpenSSL em vez de .pfx, porque é provável que o suporte para certificados .pfx seja removido no futuro.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificados de gestão no Windows/Mac/Linux (OpenSSL)
Pode utilizar o [OpenSSL](https://www.openssl.org/) para criar o seu certificado de gestão. É necessário criar dois certificados, um `.cer` para o servidor (um `.pem` ficheiro) e outro para o cliente (um ficheiro). Para criar `.pem` o ficheiro, execute:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Para criar `.cer` o certificado, execute:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Para obter mais informações sobre certificados Azure, consulte a visão geral dos [Certificados para os Serviços Azure Cloud](cloud-services-certs-create.md). Para obter uma descrição completa dos parâmetros [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html)OpenSSL, consulte a documentação em .

Depois de criar estes `.cer` ficheiros, faça o upload do ficheiro para o Azure. No [portal Azure,][management-portal]no separador **Definições,** selecione **Upload**. Repare onde guardou `.pem` o ficheiro.

Depois de obter o seu ID de `.cer` subscrição, crie um certificado e carregue o ficheiro para o Azure, ligue-se ao ponto final da gestão Azure. Ligue-se passando o ID `.pem` de subscrição e o caminho para o ficheiro para **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` está um objeto **ServiceManagementService.** A classe **ServiceManagementService** é a classe primária usada para gerir os serviços Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificados de gestão no Windows (MakeCert)
Pode criar um certificado de gestão auto-assinado na sua máquina utilizando `makecert.exe`. Abra um pedido de **comando do Estúdio Visual** como **administrador** e utilize o seguinte comando, substituindo *o AzureCertificate* pelo nome do certificado que pretende utilizar:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

O comando `.cer` cria o ficheiro e instala-o na loja de certificados **Pessoais.** Para mais informações, consulte a visão geral dos [Certificados para os Serviços Azure Cloud.](cloud-services-certs-create.md)

Depois de criar o `.cer` certificado, faça o upload do ficheiro para o Azure. No [portal Azure,][management-portal]no separador **Definições,** selecione **Upload**.

Depois de obter o seu ID de `.cer` subscrição, crie um certificado e carregue o ficheiro para o Azure, ligue-se ao ponto final da gestão Azure. Ligue-se passando o ID de subscrição e a localização do certificado na sua loja de certificados **pessoais** para **ServiceManagementService** (novamente, substitua o *AzureCertificate* pelo nome do seu certificado).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` está um objeto **ServiceManagementService.** A classe **ServiceManagementService** é a classe primária usada para gerir os serviços Azure.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Lista de locais disponíveis
Para listar os locais disponíveis para hospedagem de **serviços,\_** utilize o método de localização da lista.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Quando cria um serviço de serviço de cloud ou de armazenamento, precisa de fornecer uma localização válida. O método de **localização da lista\_** retorna sempre uma lista atualizada dos locais atualmente disponíveis. A partir desta escrita, os locais disponíveis são:

* Europa ocidental
* Europa do Norte
* Ásia Sudeste
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
Quando se cria uma aplicação e a executa em Azure, o código e a configuração juntos são chamados de [serviço de nuvem][cloud service]Azure . (Era conhecido como um *serviço hospedado* em lançamentos anteriores do Azure.) Pode utilizar o método de **serviço criado\_\_** para criar um novo serviço hospedado. Crie o serviço fornecendo um nome de serviço hospedado (que deve ser único em Azure), uma etiqueta (codificada automaticamente para base64), uma descrição e uma localização.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Pode listar todos os serviços hospedados para a sua subscrição com o método de **serviços hospedados\_\_** na lista.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Para obter informações sobre um determinado serviço hospedado, passe o nome de serviço hospedado para o método de propriedades de **\_serviço\_\_hospedado.**

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Depois de criar um serviço na nuvem, desloque o seu código para o serviço com o método de **implementação de\_criação.**

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Eliminar um serviço de nuvem
Pode eliminar um serviço na nuvem, passando o nome de serviço para o método de **serviço de\_exclusão.\_**

    sms.delete_hosted_service('myhostedservice')

Antes de poder eliminar um serviço, todas as implementações para o serviço devem ser eliminadas primeiro. Para mais informações, consulte [Eliminar uma implementação](#DeleteDeployment).

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Eliminar uma implantação
Para eliminar uma implementação, utilize o método de **eliminação.\_** O exemplo que se segue `v1`mostra como eliminar uma implantação denominada:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Criar um serviço de armazenamento
Um [serviço de armazenamento](../storage/common/storage-create-storage-account.md) dá-lhe acesso a bolhas Azure, [mesas](../storage/blobs/storage-python-how-to-use-blob-storage.md)e [filas.](../storage/queues/storage-python-how-to-use-queue-storage.md) [tables](../cosmos-db/table-storage-how-to-use-python.md) Para criar um serviço de armazenamento, precisa de um nome para o serviço (entre 3 e 24 caracteres minúsculos e único dentro do Azure). Também precisa de uma descrição, uma etiqueta (até 100 caracteres, codificada automaticamente para base64) e uma localização. O exemplo que se segue mostra como criar um serviço de armazenamento especificando uma localização:

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

No exemplo anterior, o estado da operação da conta de **\_\_armazenamento de criação** pode ser recuperado através da passagem do resultado devolvido através da **criação\_\_** de conta de armazenamento para o método de estado de **funcionamento.\_\_** 

Pode listar as suas contas de armazenamento e as suas propriedades com o método das contas de **armazenamento\_\_da lista.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Eliminar um serviço de armazenamento
Para eliminar um serviço de armazenamento, passe o nome do serviço de armazenamento para o método da **conta\_de armazenamento.\_** A eliminação de um serviço de armazenamento elimina todos os dados armazenados no serviço (bolhas, mesas e filas).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Lista de sistemas operativos disponíveis
Para listar os sistemas operativos disponíveis para serviços de hospedagem, utilize o método dos **sistemas operativos\_\_da lista.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Em alternativa, pode utilizar o método de famílias do **sistema\_\_\_operativo da lista,** que agrupa os sistemas operativos por família.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Criar uma imagem do sistema operativo
Para adicionar uma imagem do sistema operativo ao repositório de imagem, utilize o método de **imagem de\_adicionar os.\_**

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

Para listar as imagens do sistema operativo disponíveis, utilize o método **de imagens da lista.\_\_** Inclui todas as imagens da plataforma e imagens do utilizador.

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

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Eliminar uma imagem do sistema operativo
Para eliminar uma imagem do utilizador, utilize o método **de apagar\_a\_imagem.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Criar uma máquina virtual
Para criar uma máquina virtual, primeiro é necessário criar um serviço de [nuvem.](#CreateCloudService) Em seguida, crie a implantação da máquina virtual utilizando o método de implantação da **\_máquina\_\_virtual.**

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

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Eliminar uma máquina virtual
Para eliminar uma máquina virtual, elimina primeiro a implementação utilizando o método de **eliminação.\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

O serviço de nuvem pode então ser eliminado utilizando o método de **serviço de\_exclusão.\_**

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Criar uma máquina virtual a partir de uma imagem de máquina virtual capturada
Para capturar uma imagem VM, primeiro ligue para o método de **imagem vm\_\_de captura.**

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

Para se certificar de que captou com sucesso a imagem, utilize a **lista\_de imagens VM\_** API. Certifique-se de que a sua imagem está visualizada nos resultados.

    images = sms.list_vm_images()

Para finalmente criar a máquina virtual utilizando a imagem capturada, use o método de implementação da **máquina\_\_\_virtual** como antes, mas desta vez passe no vm_image_name em vez disso.

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

Para saber mais sobre como capturar uma máquina virtual Linux no modelo de implementação clássico, consulte [Capture a Linux virtual machine](../virtual-machines/linux/classic/capture-image-classic.md).

Para saber mais sobre como capturar uma máquina virtual do Windows no modelo de implementação clássico, consulte [Capture uma máquina virtual Windows](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="next-steps"></a><a name="What's Next"> </a>Passos seguintes
Agora que aprendeu o básico da gestão de serviços, pode aceder à documentação completa de referência da [API para o SDK Azure Python](https://azure-sdk-for-python.readthedocs.org/) e executar tarefas complexas facilmente para gerir a sua aplicação Python.

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
[svc-mgmt-rest-api]: https://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/azure/cloud-services/
