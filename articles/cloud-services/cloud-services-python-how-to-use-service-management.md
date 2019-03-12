---
title: Utilizar o serviço Gestão de API (Python) - guia de recursos
description: Saiba como executar tarefas de gestão comuns do serviço do Python.
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
ms.openlocfilehash: 573c6d3ded8fea58e0c9ba1afa7da2d8dd0fce91
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531845"
---
# <a name="use-service-management-from-python"></a>Utilize a gestão de serviço do Python
Este guia mostra como executar tarefas de gestão comuns do serviço do Python. O **ServiceManagementService** classe na [Azure SDK para Python](https://github.com/Azure/azure-sdk-for-python) suporta acesso programático a muitas das funcionalidades de serviços relacionados com a gestão que está disponível no [Azure Portal][management-portal]. Pode utilizar esta funcionalidade para criar, atualizar e eliminar serviços cloud, implementações, os serviços de gestão de dados e as máquinas virtuais. Essa funcionalidade pode ser útil para criar aplicativos que precisam de acesso programático a gestão de serviço.

## <a name="WhatIs"> </a>O que é a gestão de serviço?
API de gestão de serviço do Azure fornece acesso programático a muitas das funcionalidades de gestão de serviços disponíveis através da [portal do Azure][management-portal]. Pode utilizar o Azure SDK para Python para gerir os seus serviços cloud e as contas de armazenamento.

Para utilizar a API de gestão de serviço, precisa [criar uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Conceitos
O Azure SDK para Python encapsula a [API da gestão de serviço][svc-mgmt-rest-api], que é uma API REST. Todas as operações API são executadas sobre SSL e mutuamente autenticadas utilizando certificados X.509 v3. O serviço de gestão pode ser acedido a partir de dentro de um serviço em execução no Azure. Também podem ser acedido diretamente através da Internet a partir de qualquer aplicação que pode enviar um pedido HTTPS e receber uma resposta HTTPS.

## <a name="Installation"> </a>Instalação
Todas as funcionalidades descritas neste artigo estão disponíveis no `azure-servicemanagement-legacy` pacote, que pode instalar através do pip. Para obter mais informações sobre a instalação (por exemplo, se estiver familiarizado com o Python), consulte [instalar o Python e o Azure SDK](../python-how-to-install.md).

## <a name="Connect"> </a>Ligar à gestão de serviço
Para ligar ao ponto final de gestão do serviço, terá do ID de subscrição do Azure e um certificado de gestão válido. Pode obter o seu ID de subscrição através da [portal do Azure][management-portal].

> [!NOTE]
> Agora pode utilizar certificados criados com OpenSSL quando em execução no Windows. Python 2.7.4 ou posterior é necessária. Recomendamos que utilize OpenSSL, em vez de. pfx, porque o suporte para certificados. pfx é suscetível de ser removida no futuro.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificados de gerenciamento no Windows/Mac/Linux (OpenSSL)
Pode usar [OpenSSL](https://www.openssl.org/) para criar o seu certificado de gestão. Precisa criar dois certificados, um para o servidor (um `.cer` arquivo) e outro para o cliente (uma `.pem` ficheiro). Para criar o `.pem` de ficheiros, execute:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Para criar o `.cer` de certificado, execute:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Para obter mais informações sobre certificados do Azure, consulte [descrição geral de certificados para serviços Cloud do Azure](cloud-services-certs-create.md). Para obter uma descrição completa dos parâmetros de OpenSSL, consulte a documentação em [ https://www.openssl.org/docs/apps/openssl.html ](https://www.openssl.org/docs/apps/openssl.html).

Depois de criar estes ficheiros, carregar o `.cer` ficheiro para o Azure. Na [portal do Azure][management-portal], no **definições** separador, selecione **carregar**. Nota onde guardou o `.pem` ficheiro.

Depois de obter o seu ID de subscrição, crie um certificado e carregue o `.cer` de ficheiros para o Azure, ligue-se ao ponto final de gestão do Azure. Ligar ao transmitir o ID de subscrição e o caminho para o `.pem` do ficheiro para **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um **ServiceManagementService** objeto. O **ServiceManagementService** classe é a classe principal utilizada para gerir os serviços do Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificados de gerenciamento no Windows (MakeCert)
Pode criar um certificado de gestão autoassinado no seu computador usando `makecert.exe`. Abra um **prompt de comando do Visual Studio** como um **administrador** e utilize o seguinte comando, substituindo *AzureCertificate* com o nome do certificado que pretende utilizar:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

O comando cria o `.cer` de ficheiros e instala-na **pessoais** arquivo de certificados. Para obter mais informações, consulte [descrição geral de certificados para serviços Cloud do Azure](cloud-services-certs-create.md).

Depois de criar o certificado, carregar o `.cer` ficheiro para o Azure. Na [portal do Azure][management-portal], no **definições** separador, selecione **carregar**.

Depois de obter o seu ID de subscrição, crie um certificado e carregue o `.cer` de ficheiros para o Azure, ligue-se ao ponto final de gestão do Azure. Ligar ao transmitir o ID de subscrição e a localização do certificado no seu **pessoais** arquivo de certificados para **ServiceManagementService** (mais uma vez, substitua *AzureCertificate* com o nome do seu certificado).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um **ServiceManagementService** objeto. O **ServiceManagementService** classe é a classe principal utilizada para gerir os serviços do Azure.

## <a name="ListAvailableLocations"> </a>Lista de localizações disponíveis
Para listar as localizações que estão disponíveis para serviços de alojamento, utilize o **lista\_localizações** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Quando cria um serviço cloud ou o serviço de armazenamento, terá de fornecer uma localização válida. O **lista\_localizações** método sempre retorna uma lista atualizada das localizações disponíveis atualmente. No momento da elaboração deste artigo, as localizações disponíveis são:

* Europa Ocidental
* Europa do Norte
* Sudeste Asiático
* Ásia Oriental
* EUA Central
* EUA Centro-Norte
* E.U.A. Centro-Sul
* EUA Oeste
* EUA Leste
* Leste do Japão
* Oeste do Japão
* Sul do Brasil
* Leste da Austrália
* Sudeste da Austrália

## <a name="CreateCloudService"> </a>Criar um serviço cloud
Quando cria uma aplicação e executá-lo no Azure, o código e a configuração em conjunto são chamados do Azure [serviço em nuvem][cloud service]. (Era conhecido como um *serviço alojado* nas versões anteriores do Azure.) Pode utilizar o **crie\_alojado\_serviço** serviço hospedado do método para criar um novo. Crie o serviço, fornecendo um nome de serviço alojado (que tem de ser exclusivo no Azure), uma etiqueta (automaticamente codificado por base64), uma descrição e uma localização.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Pode listar todos os serviços alojados para a sua subscrição com o **lista\_alojado\_serviços** método.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Para obter informações sobre um determinado serviço hospedado, passe o nome de serviço hospedado para o **Obtenha\_alojado\_service\_propriedades** método.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Depois de criar um serviço em nuvem, implemente o código para o serviço com o **crie\_implementação** método.

## <a name="DeleteCloudService"> </a>Eliminar um serviço em nuvem
Pode eliminar um serviço em nuvem, passando o nome do serviço para o **elimine\_alojado\_serviço** método.

    sms.delete_hosted_service('myhostedservice')

Antes de poder eliminar um serviço, têm de ser eliminadas todas as implementações para o serviço. Para obter mais informações, consulte [eliminar uma implementação](#DeleteDeployment).

## <a name="DeleteDeployment"> </a>Eliminar uma implementação
Para eliminar uma implementação, utilize o **elimine\_implementação** método. O exemplo seguinte mostra como eliminar uma implementação designada `v1`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Criar um serviço de armazenamento
R [serviço de armazenamento](../storage/common/storage-create-storage-account.md) permite-lhe aceder ao Azure [blobs](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabelas](../cosmos-db/table-storage-how-to-use-python.md), e [filas](../storage/queues/storage-python-how-to-use-queue-storage.md). Para criar um serviço de armazenamento, precisa de um nome para o serviço (entre 3 e 24 carateres em minúsculas e exclusivo no Azure). Terá também uma descrição, uma etiqueta (máximo de 100 carateres, automaticamente codificados em base64) e uma localização. O exemplo seguinte mostra como criar um serviço de armazenamento ao especificar uma localização:

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

No exemplo anterior, o estado do **crie\_armazenamento\_conta** operação pode ser obtida ao transmitir o resultado devolvido pelo **criar\_armazenamento\_ conta** para o **Obtenha\_operação\_estado** método. 

Pode listar as contas de armazenamento e as respetivas propriedades com o **lista\_armazenamento\_contas** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Eliminar um serviço de armazenamento
Para eliminar um serviço de armazenamento, transmitir o nome do serviço de armazenamento para o **elimine\_armazenamento\_conta** método. Eliminar um serviço de armazenamento elimina todos os dados armazenados no serviço (blobs, tabelas e filas).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Sistemas operativos disponíveis de lista
Para listar os sistemas operativos que estão disponíveis para serviços de alojamento, utilize o **lista\_operacional\_sistemas** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Em alternativa, pode utilizar o **lista\_operacional\_system\_famílias** método, que os sistemas operativos por família de grupos.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Criar uma imagem de sistema operativo
Para adicionar uma imagem do sistema operativo para o repositório de imagens, utilize o **adicione\_SO\_imagem** método.

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

Para listar as imagens de sistema operativo que estão disponíveis, utilize o **lista\_SO\_imagens** método. Inclui todas as imagens de plataforma e imagens do utilizador.

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

## <a name="DeleteVMImage"> </a>Eliminar uma imagem de sistema operativo
Para eliminar uma imagem de utilizador, utilize o **elimine\_SO\_imagem** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Criar uma máquina virtual
Para criar uma máquina virtual, tem primeiro de criar uma [serviço em nuvem](#CreateCloudService). Em seguida, criar a implementação da máquina virtual com o **criar\_virtual\_máquina\_implementação** método.

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

## <a name="DeleteVM"> </a>Eliminar uma máquina virtual
Para eliminar uma máquina virtual, primeiro eliminar a implementação utilizando o **elimine\_implementação** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

O serviço em nuvem, em seguida, pode ser eliminado ao utilizar o **elimine\_alojado\_serviço** método.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Criar uma máquina virtual a partir de uma imagem de máquina de virtual capturada
Para capturar uma imagem de VM, primeiro de chamar o **capturar\_vm\_imagem** método.

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

Para certificar-se de que capturada com êxito a imagem, utilize o **lista\_vm\_imagens** API. Certifique-se de que sua imagem é apresentada nos resultados.

    images = sms.list_vm_images()

Para criar, finalmente, a máquina virtual utilizando a imagem capturada, utilize o **crie\_virtual\_máquina\_implementação** método como antes, mas desta vez passar o vm_image_name em vez disso.

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

Para saber mais sobre como capturar uma máquina virtual do Linux no modelo de implementação clássica, veja [capturar uma máquina virtual Linux](../virtual-machines/linux/classic/capture-image-classic.md).

Para saber mais sobre como capturar uma máquina virtual do Windows no modelo de implementação clássica, veja [capturar uma máquina virtual do Windows](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"> </a>Passos seguintes
Agora que aprendeu as noções básicas da gestão de serviço, pode aceder a [documentação de referência de API completa para o SDK Python](https://azure-sdk-for-python.readthedocs.org/) e realizar tarefas complexas facilmente para gerir a sua aplicação de Python.

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
