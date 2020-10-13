---
title: Serviços cloud e certificados de gestão Microsoft Docs
description: Saiba como criar e implementar certificados para serviços na nuvem e para autenticar com a gestão API em Azure.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 08ce69856dd36b6029297109fcb8610b856c8b98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142371"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Certificates overview for Azure Cloud Services (Descrição geral dos certificados para os Serviços Cloud do Azure)
Os certificados são utilizados no Azure para serviços na nuvem[(certificados](#what-are-service-certificates)de serviço) e para autenticação com a API de gestão[(certificados de gestão).](#what-are-management-certificates) Este tópico dá uma visão geral de [create](#create) ambos os tipos de certificados, como criá-los e implantá-los para Azure.

Os certificados utilizados no Azure são certificados x.509 v3 e podem ser assinados por outro certificado de confiança ou podem ser auto-assinados. Um certificado auto-assinado é assinado pelo seu próprio criador, pelo que não é de confiança por defeito. A maioria dos browsers pode ignorar este problema. Só deve utilizar certificados auto-assinados quando desenvolver e testar os seus serviços na nuvem. 

Os certificados utilizados pela Azure podem conter uma chave pública. Os certificados têm uma impressão digital que fornece um meio para identificá-los de forma inequívoca. Esta impressão digital é utilizada no [ficheiro de configuração](cloud-services-configure-ssl-certificate-portal.md) Azure para identificar qual o certificado que um serviço de nuvem deve utilizar. 

>[!Note]
>A Azure Cloud Services não aceita certificado encriptado AES256-SHA256.

## <a name="what-are-service-certificates"></a>O que são os certificados de serviço?
Os certificados de serviço são associados aos serviços cloud e permitem uma comunicação segura com os mesmos. Por exemplo, se implementou uma função web, pretende fornecer um certificado que possa autenticar um ponto final HTTPS exposto. Os certificados de serviço, definidos na sua definição de serviço, são automaticamente implantados na máquina virtual que está a executar uma instância da sua função. 

Pode carregar certificados de serviço no Azure através do portal do Azure ou do modelo de implementação clássico. Os certificados de serviço são associados a um serviço cloud específico. São atribuídos a uma implementação no ficheiro de definição do serviço.

Os certificados de serviço podem ser geridos separadamente dos seus serviços, e podem ser geridos por diferentes indivíduos. Por exemplo, um desenvolvedor pode enviar um pacote de serviço que se refere a um certificado que um gestor de TI já carregou para a Azure. Um gestor de TI pode gerir e renovar esse certificado (ao alterar a configuração do serviço) sem precisar de carregar um novo pacote de serviço. A atualização sem um novo pacote de serviços é possível porque o nome lógico, o nome da loja e a localização do certificado estão no ficheiro de definição de serviço e enquanto a impressão digital do certificado é especificada no ficheiro de configuração de serviço. Para atualizar o certificado, basta carregar um novo certificado e alterar o valor do thumbprint no ficheiro de configuração do serviço.

>[!Note]
>O artigo [de Cloud Services FAQ - Configuração e Gestão](cloud-services-configuration-and-management-faq.md) tem algumas informações úteis sobre certificados.

## <a name="what-are-management-certificates"></a>O que são os certificados de gestão?
Os certificados de gestão permitem-lhe efetuar autenticações com o modelo de implementação clássico. Muitos programas e ferramentas (por exemplo, o Visual Studio ou o SDK do Azure) utilizam estes certificados para automatizar a configuração e a implementação de vários serviços do Azure. Estes não estão realmente relacionados com serviços na nuvem. 

> [!WARNING]
> Cuidado! Estes tipos de certificados permitem a quem autenticar com eles a gestão da subscrição a que está associado. 
> 
> 

### <a name="limitations"></a>Limitações
Existe um limite de 100 certificados de gestão por subscrição. Existe também um limite de 100 certificados de gestão para todas as subscrições ao abrigo do ID do utilizador de um administrador de serviço específico. Se o ID do utilizador do administrador de conta já tiver sido utilizado para adicionar 100 certificados de gestão e houver necessidade de mais certificados, pode adicionar um coadministrador para adicionar os certificados adicionais. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado auto-assinado
Pode utilizar qualquer ferramenta disponível para criar um certificado auto-assinado desde que cumpram estas definições:

* Um certificado X.509.
* Contém uma chave pública.
* Criado para troca de chaves (ficheiro.pfx).
* O nome do sujeito deve coincidir com o domínio utilizado para aceder ao serviço de nuvem.

    > Não é possível adquirir um certificado TLS/SSL para o domínio cloudapp.net (ou para qualquer domínio relacionado com o Azure); O nome do sujeito do certificado deve corresponder ao nome de domínio personalizado utilizado para aceder à sua aplicação. Por exemplo, **contoso.net**, não **contoso.cloudapp.net**.

* Encriptação mínima de 2048 bits.
* **Apenas Certificado de Serviço**: O certificado do lado do cliente deve residir na loja *de certificados pessoal.*

Existem duas maneiras fáceis de criar um certificado no Windows, com o `makecert.exe` utilitário, ou IIS.

### <a name="makecertexe"></a>Makecert.exe
Esta utilidade foi depreciada e já não está documentada aqui. Para mais informações, consulte [este artigo da MSDN.](/windows/desktop/SecCrypto/makecert)

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Se pretender utilizar o certificado com um endereço IP em vez de um domínio, utilize o endereço IP no parâmetro -DnsName.


Se pretender utilizar este [certificado com o portal de gestão,](../azure-api-management-certs.md)exporte-o para um ficheiro **.cer:**

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Serviços de Informação de Internet (IIS)
Há muitas páginas na internet que cobrem como fazê-lo com o IIS. [Aqui](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) está um grande que descobri que acho que explica bem. 

### <a name="linux"></a>Linux
[Este](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artigo descreve como criar certificados com SSH.

## <a name="next-steps"></a>Passos seguintes
[Faça o upload do seu certificado de serviço para o portal Azure](cloud-services-configure-ssl-certificate-portal.md).

Faça o upload de um [certificado de API](../azure-api-management-certs.md) de gestão para o portal Azure.




