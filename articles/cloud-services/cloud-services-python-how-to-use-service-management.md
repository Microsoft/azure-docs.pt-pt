---
title: Usar o API de Gerenciamento de Serviços (Python) – guia de recursos
description: Saiba como executar programaticamente tarefas comuns de gerenciamento de serviços do Python.
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: 50501413a63921a9a34be1c04ed259990922b686
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141471"
---
# <a name="use-service-management-from-python"></a>Usar o gerenciamento de serviços do Python
Este guia mostra como executar programaticamente tarefas comuns de gerenciamento de serviços do Python. A classe **ServiceManagementService** no [SDK do Azure para Python](https://github.com/Azure/azure-sdk-for-python) dá suporte ao acesso programático a grande parte da funcionalidade relacionada ao gerenciamento de serviços que está disponível no [portal do Azure][management-portal]. Você pode usar essa funcionalidade para criar, atualizar e excluir serviços de nuvem, implantações, serviços de gerenciamento de dados e máquinas virtuais. Essa funcionalidade pode ser útil na criação de aplicativos que precisam de acesso programático ao gerenciamento de serviços.

## <a name="WhatIs"> </a>O que é o gerenciamento de serviços?
O API de Gerenciamento de Serviços do Azure fornece acesso programático a grande parte da funcionalidade de gerenciamento de serviços disponível por meio do [portal do Azure][management-portal]. Você pode usar o SDK do Azure para Python para gerenciar seus serviços de nuvem e contas de armazenamento.

Para usar o API de Gerenciamento de Serviços, você precisa [criar uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Conceitos do
O SDK do Azure para Python encapsula o [API de gerenciamento de serviços][svc-mgmt-rest-api], que é uma API REST. Todas as operações de API são executadas por SSL e mutuamente autenticadas usando certificados X. 509 v3. O serviço de gerenciamento pode ser acessado de dentro de um serviço em execução no Azure. Ele também pode ser acessado diretamente pela Internet de qualquer aplicativo que possa enviar uma solicitação HTTPS e receber uma resposta HTTPS.

## <a name="Installation"> </a>Instalação do
Todos os recursos descritos neste artigo estão disponíveis no `azure-servicemanagement-legacy` pacote, que você pode instalar usando Pip. Para obter mais informações sobre a instalação (por exemplo, se você for novo no Python), consulte [instalar o Python e o SDK do Azure](/azure/python/python-sdk-azure-install).

## <a name="Connect"> </a>Conectar-se ao gerenciamento de serviços
Para se conectar ao ponto de extremidade do gerenciamento de serviços, você precisa de sua ID de assinatura do Azure e um certificado de gerenciamento válido. Você pode obter sua ID de assinatura por meio do [portal do Azure][management-portal].

> [!NOTE]
> Agora você pode usar certificados criados com OpenSSL ao executar no Windows. O Python 2.7.4 ou posterior é necessário. Recomendamos que você use o OpenSSL em vez do. pfx, pois é provável que o suporte para certificados. pfx seja removido no futuro.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificados de gerenciamento no Windows/Mac/Linux (OpenSSL)
Você pode usar o [OpenSSL](https://www.openssl.org/) para criar seu certificado de gerenciamento. Você precisa criar dois certificados, um para o servidor (um `.cer` arquivo) e outro para o cliente (um `.pem` arquivo). Para criar o `.pem` arquivo, execute:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Para criar o `.cer` certificado, execute:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Para obter mais informações sobre certificados do Azure, consulte [visão geral de certificados para serviços de nuvem do Azure](cloud-services-certs-create.md). Para obter uma descrição completa dos parâmetros do OpenSSL, consulte a [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html)documentação em.

Depois de criar esses arquivos, carregue o `.cer` arquivo no Azure. Na [portal do Azure][management-portal], na guia **configurações** , selecione **carregar**. Observe onde você salvou o `.pem` arquivo.

Depois de obter sua ID de assinatura, crie um certificado e carregue o `.cer` arquivo no Azure, conecte-se ao ponto de extremidade de gerenciamento do Azure. Conecte-se passando a ID da assinatura e o caminho `.pem` para o arquivo para **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um objeto **ServiceManagementService** . A classe **ServiceManagementService** é a classe primária usada para gerenciar os serviços do Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificados de gerenciamento no Windows (MakeCert)
Você pode criar um certificado de gerenciamento autoassinado em seu computador usando `makecert.exe`o. Abra um **prompt de comando do Visual Studio** como **administrador** e use o comando a seguir, substituindo *azurecertificate pelo* pelo nome do certificado que você deseja usar:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

O comando cria o `.cer` arquivo e o instala no repositório de certificados **pessoal** . Para obter mais informações, consulte [visão geral de certificados para serviços de nuvem do Azure](cloud-services-certs-create.md).

Depois de criar o certificado, carregue o `.cer` arquivo no Azure. Na [portal do Azure][management-portal], na guia **configurações** , selecione **carregar**.

Depois de obter sua ID de assinatura, crie um certificado e carregue o `.cer` arquivo no Azure, conecte-se ao ponto de extremidade de gerenciamento do Azure. Conecte-se passando a ID da assinatura e o local do certificado em seu repositório de certificados **pessoal** para **ServiceManagementService** (novamente, substitua *azurecertificate pelo* pelo nome do seu certificado).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um objeto **ServiceManagementService** . A classe **ServiceManagementService** é a classe primária usada para gerenciar os serviços do Azure.

## <a name="ListAvailableLocations"> </a>Listar locais disponíveis
Para listar os locais que estão disponíveis para serviços de hospedagem, use o método **List\_Locations** .

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Ao criar um serviço de nuvem ou serviço de armazenamento, você precisa fornecer um local válido. O **método\_List Locations** sempre retorna uma lista atualizada dos locais disponíveis no momento. No momento da redação deste artigo, os locais disponíveis são:

* Europa Ocidental
* Europa do Norte
* Sudeste Asiático
* Ásia Oriental
* EUA Central
* EUA Centro-Norte
* E.U.A. Centro-Sul
* EUA Oeste
* East US
* Leste do Japão
* Oeste do Japão
* Sul do Brasil
* Leste da Austrália
* Sudeste da Austrália

## <a name="CreateCloudService"> </a>Criar um serviço de nuvem
Quando você cria um aplicativo e o executa no Azure, o código e a configuração juntos são chamados de [serviço de nuvem][cloud service]do Azure. (Ele era conhecido como um *serviço hospedado* em versões anteriores do Azure.) Você pode usar o **método\_criar\_serviço hospedado** para criar um novo serviço hospedado. Crie o serviço fornecendo um nome de serviço hospedado (que deve ser exclusivo no Azure), um rótulo (codificado automaticamente para Base64), uma descrição e um local.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Você pode listar todos os serviços hospedados para sua assinatura com o método **\_listar serviços\_hospedados** .

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Para obter informações sobre um serviço hospedado específico, passe o nome do serviço hospedado para o método **\_obter\_Propriedades do serviço hospedado\_** .

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Depois de criar um serviço de nuvem, implante seu código no serviço com o **método\_criar implantação** .

## <a name="DeleteCloudService"> </a>Excluir um serviço de nuvem
Você pode excluir um serviço de nuvem passando o nome do serviço para **o\_método\_excluir serviço hospedado** .

    sms.delete_hosted_service('myhostedservice')

Antes que você possa excluir um serviço, todas as implantações para o serviço devem ser excluídas primeiro. Para obter mais informações, consulte [excluir uma implantação](#DeleteDeployment).

## <a name="DeleteDeployment"> </a>Excluir uma implantação
Para excluir uma implantação, use o **método\_excluir implantação** . O exemplo a seguir mostra como excluir uma implantação chamada `v1`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Criar um serviço de armazenamento
Um [serviço de armazenamento](../storage/common/storage-create-storage-account.md) fornece acesso a [BLOBs](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabelas](../cosmos-db/table-storage-how-to-use-python.md)e [filas](../storage/queues/storage-python-how-to-use-queue-storage.md)do Azure. Para criar um serviço de armazenamento, você precisa de um nome para o serviço (entre 3 e 24 caracteres minúsculos e exclusivo no Azure). Você também precisa de uma descrição, um rótulo (até 100 caracteres, codificado automaticamente para Base64) e um local. O exemplo a seguir mostra como criar um serviço de armazenamento especificando um local:

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

No exemplo anterior, o status da operação **\_criar conta de\_armazenamento** pode ser recuperado passando o resultado retornado pela **\_criação da conta de\_armazenamento** para o **Get\_método\_de status da operação** . 

Você pode listar suas contas de armazenamento e suas propriedades com o método **\_List Storage\_** accounts.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Excluir um serviço de armazenamento
Para excluir um serviço de armazenamento, passe o nome do serviço de armazenamento para o método **\_excluir conta de armazenamento\_** . A exclusão de um serviço de armazenamento exclui todos os dados armazenados no serviço (BLOBs, tabelas e filas).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Listar sistemas operacionais disponíveis
Para listar os sistemas operacionais que estão disponíveis para hospedar serviços, use **o\_método\_List Operating Systems** .

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Como alternativa, você pode usar o **método\_listar\_famílias\_** de sistemas operacionais, que agrupa os sistemas operacionais por família.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Criar uma imagem do sistema operacional
Para adicionar uma imagem do sistema operacional ao repositório de imagens, use **o\_método\_add os Image** .

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

Para listar as imagens do sistema operacional que estão disponíveis, use o método **\_List os\_images** . Ele inclui todas as imagens de plataforma e imagens de usuário.

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

## <a name="DeleteVMImage"> </a>Excluir uma imagem do sistema operacional
Para excluir uma imagem de usuário, use **o\_método\_delete os Image** .

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Criar uma máquina virtual
Para criar uma máquina virtual, primeiro você precisa criar um [serviço de nuvem](#CreateCloudService). Em seguida, crie a implantação da máquina virtual usando o método **criar\_implantação de máquina\_\_virtual** .

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

## <a name="DeleteVM"> </a>Excluir uma máquina virtual
Para excluir uma máquina virtual, primeiro exclua a implantação usando o método **excluir\_implantação** .

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

O serviço de nuvem pode ser excluído usando o método **excluir\_serviço\_hospedado** .

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Criar uma máquina virtual por meio de uma imagem capturada da máquina virtual
Para capturar uma imagem de VM, primeiro você chama **o\_método\_Capture VM Image** .

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

Para certificar-se de que você capturou a imagem com êxito, use a API **listar\_imagens de VM\_** . Verifique se a imagem é exibida nos resultados.

    images = sms.list_vm_images()

Para, por fim, criar a máquina virtual usando a imagem capturada, use o método **criar\_implantação de máquina\_\_virtual** como antes, mas desta vez passe o vm_image_name.

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

Para saber mais sobre como capturar uma máquina virtual do Linux no modelo de implantação clássico, consulte [capturar uma máquina virtual do Linux](../virtual-machines/linux/classic/capture-image-classic.md).

Para saber mais sobre como capturar uma máquina virtual do Windows no modelo de implantação clássico, consulte [capturar uma máquina virtual do Windows](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"> </a>Passos seguintes
Agora que você aprendeu os fundamentos do gerenciamento de serviços, é possível acessar a [documentação de referência da API completa para o SDK do Python do Azure](https://azure-sdk-for-python.readthedocs.org/) e executar tarefas complexas facilmente para gerenciar seu aplicativo Python.

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
