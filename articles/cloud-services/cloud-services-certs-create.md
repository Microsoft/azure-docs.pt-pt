---
title: Serviços de Nuvem e certificados de gestão / Microsoft Docs
description: Saiba como criar e usar certificados com o Microsoft Azure
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: cf2106302064df5ede02d18f253436047a5d33d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024613"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Certificates overview for Azure Cloud Services (Descrição geral dos certificados para os Serviços Cloud do Azure)
Os certificados são utilizados em Azure para serviços na nuvem[(certificados](#what-are-service-certificates)de serviço) e para autenticação com a gestão API[(certificados de gestão).](#what-are-management-certificates) Este tópico dá uma visão geral de ambos os tipos de certificados, como [criá-los](#create) e implantá-los para O Azure.

Os certificados utilizados no Azure são certificados x.509 v3 e podem ser assinados por outro certificado de confiança ou podem ser auto-assinados. Um certificado auto-assinado é assinado pelo seu próprio criador, pelo que não é de confiança por defeito. A maioria dos browsers pode ignorar este problema. Só deve utilizar certificados auto-assinados ao desenvolver e testar os seus serviços na nuvem. 

Os certificados utilizados pelo Azure podem conter uma chave pública. Os certificados têm uma impressão digital que fornece um meio de identificá-los de forma inequívoca. Esta impressão digital é utilizada no ficheiro de [configuração](cloud-services-configure-ssl-certificate-portal.md) Azure para identificar qual o certificado que um serviço na nuvem deve utilizar. 

>[!Note]
>A Azure Cloud Services não aceita certificado encriptado AES256-SHA256.

## <a name="what-are-service-certificates"></a>O que são os certificados de serviço?
Os certificados de serviço são associados aos serviços cloud e permitem uma comunicação segura com os mesmos. Por exemplo, se implementasse uma função web, quereria fornecer um certificado que pudesse autenticar um ponto final HTTPS exposto. Os certificados de serviço, definidos na sua definição de serviço, são automaticamente implantados para a máquina virtual que está a executar uma instância do seu papel. 

Pode carregar certificados de serviço no Azure através do portal do Azure ou do modelo de implementação clássico. Os certificados de serviço são associados a um serviço cloud específico. São atribuídos a uma implementação no ficheiro de definição do serviço.

Os certificados de serviço podem ser geridos separadamente dos seus serviços, e podem ser geridos por diferentes indivíduos. Por exemplo, um desenvolvedor pode carregar um pacote de serviço que se refere a um certificado que um gestor de TI já fez o upload para o Azure. Um gestor de TI pode gerir e renovar esse certificado (ao alterar a configuração do serviço) sem precisar de carregar um novo pacote de serviço. A atualização sem um novo pacote de serviço é possível porque o nome lógico, nome da loja e localização do certificado estão no ficheiro de definição de serviço e enquanto a impressão digital do certificado é especificada no ficheiro de configuração do serviço. Para atualizar o certificado, basta carregar um novo certificado e alterar o valor do thumbprint no ficheiro de configuração do serviço.

>[!Note]
>O artigo [de Configuração e Gestão](cloud-services-configuration-and-management-faq.md) de Serviços cloud tem algumas informações úteis sobre certificados.

## <a name="what-are-management-certificates"></a>O que são os certificados de gestão?
Os certificados de gestão permitem-lhe efetuar autenticações com o modelo de implementação clássico. Muitos programas e ferramentas (por exemplo, o Visual Studio ou o SDK do Azure) utilizam estes certificados para automatizar a configuração e a implementação de vários serviços do Azure. Estes não estão realmente relacionados com serviços na nuvem. 

> [!WARNING]
> Cuidado! Este tipo de certificados permitem a qualquer pessoa que autentica com eles gerir a subscrição a que estão associados. 
> 
> 

### <a name="limitations"></a>Limitações
Existe um limite de 100 certificados de gestão por subscrição. Existe também um limite de 100 certificados de gestão para todas as subscrições ao abrigo do ID de utilizador de um administrador de serviço específico. Se o ID do utilizador para o administrador da conta já tiver sido utilizado para adicionar 100 certificados de gestão e houver necessidade de mais certificados, pode adicionar um coadministrador para adicionar os certificados adicionais. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado auto-assinado
Pode utilizar qualquer ferramenta disponível para criar um certificado auto-assinado desde que adere a estas definições:

* Um certificado X.509.
* Contém uma chave pública.
* Criado para troca de chaves (ficheiro.pfx).
* O nome do assunto deve coincidir com o domínio utilizado para aceder ao serviço de nuvem.

    > Não é possível adquirir um certificado TLS/SSL para o domínio cloudapp.net (ou para qualquer domínio relacionado com o Azure); o nome do certificado deve corresponder ao nome de domínio personalizado utilizado para aceder à sua aplicação. Por exemplo, **contoso.net**, não **contoso.cloudapp.net.**

* No mínimo de encriptação de 2048 bits.
* **Certificado de Serviço Apenas:** O certificado do lado do cliente deve residir no certificado *pessoal.*

Existem duas maneiras fáceis de `makecert.exe` criar um certificado no Windows, com o utilitário, ou IIS.

### <a name="makecertexe"></a>Makecert.exe
Esta utilidade foi depreciada e já não está documentada aqui. Para mais informações, consulte este artigo da [MSDN](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Se pretender utilizar o certificado com um endereço IP em vez de um domínio, utilize o endereço IP no parâmetro -DnsName.


Se quiser utilizar este [certificado com o portal de gestão,](../azure-api-management-certs.md)exporte-o para um ficheiro **.cer:**

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Serviços de Informação de Internet (IIS)
Há muitas páginas na internet que cobrem como fazê-lo com o IIS. [Aqui](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) está uma grande que encontrei que acho que explica bem. 

### <a name="linux"></a>Linux
[Este](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artigo descreve como criar certificados com SSH.

## <a name="next-steps"></a>Passos seguintes
[Faça o upload do seu certificado de serviço para o portal Azure.](cloud-services-configure-ssl-certificate-portal.md)

Faça o upload de um [certificado de Gestão API](../azure-api-management-certs.md) para o portal Azure.




