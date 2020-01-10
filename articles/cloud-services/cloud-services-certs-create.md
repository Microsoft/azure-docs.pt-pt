---
title: Serviços de nuvem e certificados de gerenciamento | Microsoft Docs
description: Saiba como criar e usar certificados com Microsoft Azure
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 783343dd8877bdf18e783494960c3052c293cc7c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75361352"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Visão geral de certificados para serviços de nuvem do Azure
Os certificados são usados no Azure para serviços de nuvem ([certificados de serviço](#what-are-service-certificates)) e para autenticação com a API de gerenciamento ([certificados de gerenciamento](#what-are-management-certificates)). Este tópico fornece uma visão geral dos dois tipos de certificado, como [criá](#create) -los e implantá-los no Azure.

Os certificados usados no Azure são certificados x. 509 v3 e podem ser assinados por outro certificado confiável ou podem ser autoassinados. Um certificado autoassinado é assinado por seu próprio criador, portanto, ele não é confiável por padrão. A maioria dos browsers pode ignorar este problema. Você só deve usar certificados autoassinados ao desenvolver e testar seus serviços de nuvem. 

Os certificados usados pelo Azure podem conter uma chave privada ou pública. Os certificados têm uma impressão digital que fornece meios para identificá-los de forma não ambígua. Essa impressão digital é usada no [arquivo de configuração](cloud-services-configure-ssl-certificate-portal.md) do Azure para identificar qual certificado um serviço de nuvem deve usar. 

>[!Note]
>Os serviços de nuvem do Azure não aceitam o certificado criptografado AES256-SHA256.

## <a name="what-are-service-certificates"></a>O que são certificados de serviço?
Os certificados de serviço são associados aos serviços cloud e permitem uma comunicação segura com os mesmos. Por exemplo, se você implantou uma função Web, convém fornecer um certificado que possa autenticar um ponto de extremidade HTTPS exposto. Os certificados de serviço, definidos em sua definição de serviço, são automaticamente implantados na máquina virtual que está executando uma instância de sua função. 

Pode carregar certificados de serviço no Azure através do portal do Azure ou do modelo de implementação clássico. Os certificados de serviço são associados a um serviço cloud específico. São atribuídos a uma implementação no ficheiro de definição do serviço.

Os certificados de serviço podem ser gerenciados separadamente de seus serviços e podem ser gerenciados por indivíduos diferentes. Por exemplo, um desenvolvedor pode carregar um pacote de serviço que se refere a um certificado que um gerente de ti carregou anteriormente no Azure. Um gestor de TI pode gerir e renovar esse certificado (ao alterar a configuração do serviço) sem precisar de carregar um novo pacote de serviço. A atualização sem um novo pacote de serviço é possível porque o nome lógico, o nome do repositório e o local do certificado estão no arquivo de definição de serviço e enquanto a impressão digital do certificado é especificada no arquivo de configuração de serviço. Para atualizar o certificado, basta carregar um novo certificado e alterar o valor do thumbprint no ficheiro de configuração do serviço.

>[!Note]
>O artigo [perguntas frequentes sobre serviços de nuvem – configuração e gerenciamento](cloud-services-configuration-and-management-faq.md) tem algumas informações úteis sobre certificados.

## <a name="what-are-management-certificates"></a>O que são os certificados de gerenciamento?
Os certificados de gestão permitem-lhe efetuar autenticações com o modelo de implementação clássico. Muitos programas e ferramentas (por exemplo, o Visual Studio ou o SDK do Azure) utilizam estes certificados para automatizar a configuração e a implementação de vários serviços do Azure. Eles não estão realmente relacionados aos serviços de nuvem. 

> [!WARNING]
> Tenha cuidado! Esses tipos de certificados permitem que qualquer pessoa que se autentique com eles gerencie a assinatura à qual eles estão associados. 
> 
> 

### <a name="limitations"></a>Limitações
Há um limite de 100 certificados de gerenciamento por assinatura. Também há um limite de 100 certificados de gerenciamento para todas as assinaturas em uma ID de usuário específica do administrador de serviços. Se a ID de usuário para o administrador da conta já tiver sido usada para adicionar certificados de gerenciamento 100 e houver a necessidade de mais certificados, você poderá adicionar um coadministrador para adicionar os certificados adicionais. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado autoassinado
Você pode usar qualquer ferramenta disponível para criar um certificado autoassinado, desde que ele siga estas configurações:

* Um certificado X. 509.
* Contém uma chave privada.
* Criado para troca de chaves (arquivo. pfx).
* O nome da entidade deve corresponder ao domínio usado para acessar o serviço de nuvem.

    > Você não pode adquirir um certificado SSL para o cloudapp.net (ou para qualquer domínio relacionado ao Azure); o nome da entidade do certificado deve corresponder ao nome de domínio personalizado usado para acessar seu aplicativo. Por exemplo, **contoso.net**, não **contoso.cloudapp.net**.

* Mínimo de criptografia de 2048 bits.
* **Somente certificado de serviço**: o certificado do lado do cliente deve residir no repositório de certificados *pessoal* .

Há duas maneiras fáceis de criar um certificado no Windows, com o utilitário `makecert.exe` ou o IIS.

### <a name="makecertexe"></a>Makecert.exe
Este utilitário foi preterido e não está mais documentado aqui. Para obter mais informações, consulte [Este artigo do MSDN](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Se você quiser usar o certificado com um endereço IP em vez de um domínio, use o endereço IP no parâmetro-DnsName.


Se você quiser usar esse [certificado com o portal de gerenciamento](../azure-api-management-certs.md), exporte-o para um arquivo **. cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Serviços de Informação Internet (IIS)
Há muitas páginas na Internet que abordam como fazer isso com o IIS. [Aqui](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) está um ótimo artigo que acho que ele explica bem. 

### <a name="linux"></a>Linux
[Este](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artigo descreve como criar certificados com o SSH.

## <a name="next-steps"></a>Passos seguintes
[Carregue seu certificado de serviço no portal do Azure](cloud-services-configure-ssl-certificate-portal.md).

Carregue um [certificado de API de gerenciamento](../azure-api-management-certs.md) para o portal do Azure.




