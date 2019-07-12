---
title: Serviços cloud e certificados de gestão | Documentos da Microsoft
description: Saiba como criar e utilizar certificados com o Microsoft Azure
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 82d1114451b677880b0b02234546d9cdb2e743e2
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593052"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Descrição geral de certificados para serviços Cloud do Azure
Os certificados são utilizados no Azure para serviços em nuvem ([certificados de serviço](#what-are-service-certificates)) e para a autenticação com a API de gestão ([certificados de gestão](#what-are-management-certificates)). Este tópico fornece uma visão geral de ambos os tipos de certificado, como ao [criar](#create) e implementá-las para o Azure.

Certificados utilizados no Azure são x.509 v3 certificados e podem ser assinados por outro certificado fidedigno ou podem ser autoassinados. Um certificado autoassinado esteja assinado por seu próprio criador, por isso ele não é considerado fidedigno por predefinição. A maioria dos navegadores podem ignorar este problema. Só deve utilizar certificados autoassinados quando desenvolver e testar seus serviços cloud. 

Certificados utilizados pelo Azure podem conter um privado ou uma chave pública. Certificados têm um thumbprint que fornece um meio para identificá-los de maneira inequívoca. Este thumbprint é utilizado no Azure [ficheiro de configuração](cloud-services-configure-ssl-certificate-portal.md) para identificar o certificado que um serviço em nuvem deve utilizar. 

>[!Note]
>Serviços Cloud do Azure não aceita AES256 SHA256 certificados encriptados.

## <a name="what-are-service-certificates"></a>O que são certificados de serviço?
Certificados de serviço são anexados a serviços em nuvem e permitir a comunicação segura de e para o serviço. Por exemplo, se tiver implementado uma função da web, gostaria de fornecer um certificado que pode autenticar-se um ponto de final HTTPS exposto. Certificados de serviço, definidos na definição do serviço, são implementados automaticamente para a máquina virtual que está a executar uma instância da sua função. 

Pode carregar certificados de serviço para o Azure utilizando o portal do Azure ou utilizando o modelo de implementação clássica. Certificados de serviço estão associados um serviço em nuvem específico. São atribuídas a uma implementação no ficheiro de definição do serviço.

Certificados de serviço podem ser geridos separadamente dos seus serviços e podem ser geridos por diferentes pessoas. Por exemplo, um desenvolvedor pode carregar um pacote de serviço que se refere a um certificado que um gerente de TI anteriormente carregada para o Azure. Um gerente de TI pode gerir e renovar o certificado (alteração da configuração do serviço) sem a necessidade de carregar um novo pacote de serviço. Atualização sem um novo pacote de serviço é possível porque o nome lógico, o nome de arquivo e a localização do certificado está no arquivo de definição do serviço e enquanto o thumbprint do certificado é especificado no ficheiro de configuração do serviço. Para atualizar o certificado, apenas é necessário carregar um novo certificado e alterar o valor do thumbprint no ficheiro de configuração do serviço.

>[!Note]
>O [FAQ de serviços Cloud - configuração e gestão](cloud-services-configuration-and-management-faq.md) artigo tem algumas informações úteis sobre certificados.

## <a name="what-are-management-certificates"></a>O que são certificados de gestão?
Certificados de gestão permitem-lhe autenticar com o modelo de implementação clássica. Muitos programas e ferramentas (como o Visual Studio ou o SDK do Azure) utilizam estes certificados para automatizar a configuração e implementação dos vários serviços do Azure. Estes não estão realmente relacionadas a serviços em nuvem. 

> [!WARNING]
> Tenha cuidado! Esses tipos de certificados permitem que qualquer pessoa que efetua a autenticação com os mesmos para gerir a subscrição na que qual estão associados. 
> 
> 

### <a name="limitations"></a>Limitações
Existe um limite de 100 certificados de gestão por subscrição. Também existe um limite de 100 certificados de gestão para todas as subscrições com ID de utilizador. do administrador de serviço específico Se o ID de utilizador para o administrador de conta já foi utilizado para adicionar 100 certificados de gestão e há uma necessidade de mais certificados, pode adicionar um coadministrador para adicionar os certificados adicionais. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado autoassinado
Pode utilizar qualquer ferramenta disponível para criar um certificado autoassinado, desde adira a estas definições:

* Um certificado X.509.
* Contém uma chave privada.
* Criado para troca de chaves (ficheiro. pfx).
* Nome do requerente tem de corresponder o domínio utilizado para aceder ao serviço cloud.

    > Não é possível adquirir um certificado SSL para o cloudapp.net (ou para qualquer relacionados com o Azure) domínio; nome do requerente do certificado tem de corresponder ao nome de domínio personalizado utilizado para aceder à sua aplicação. Por exemplo, **contoso.net**, e não **contoso.cloudapp.net**.

* Mínimo de encriptação de 2048 bits.
* **Apenas o certificado de serviço**: Tem de residir no certificado do lado do cliente do *pessoais* arquivo de certificados.

Existem duas formas fáceis de criar um certificado no Windows, com o `makecert.exe` utilitário ou IIS.

### <a name="makecertexe"></a>Makecert.exe
Esse utilitário foi preterido e já não está documentado aqui. Para obter mais informações, consulte [este artigo MSDN](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Se pretender utilizar o certificado com um endereço IP em vez de um domínio, utilize o endereço IP no parâmetro - DnsName.


Se pretender utilizá-lo [certificado com o portal de gestão](../azure-api-management-certs.md), exportá-lo para um **. cer** ficheiro:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Serviços de informação Internet (IIS)
Existem muitas páginas na internet que abrangem como fazê-lo com o IIS. [Aqui](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) é um excelente, descobri que acho que explica bem. 

### <a name="linux"></a>Linux
[Isso](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artigo descreve como criar certificados com SSH.

## <a name="next-steps"></a>Passos Seguintes
[Carregue o certificado de serviço para o portal do Azure](cloud-services-configure-ssl-certificate-portal.md).

Carregar uma [certificado de gestão de API](../azure-api-management-certs.md) ao portal do Azure.

