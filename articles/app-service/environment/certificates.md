---
title: Associações de certificados
description: Explique vários tópicos relacionados a certificados em um Ambiente do Serviço de Aplicativo. Saiba como as associações de certificado funcionam nos aplicativos de locatário único em um ASE.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 65fc4ed25b0fd360de8e3b1439d1766485eb2e58
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688648"
---
# <a name="certificates-and-the-app-service-environment"></a>Certificados e o Ambiente do Serviço de Aplicativo 

O Ambiente do Serviço de Aplicativo (ASE) é uma implantação do serviço de Azure App que é executado em sua VNet (rede virtual) do Azure. Ele pode ser implantado com um ponto de extremidade de aplicativo acessível pela Internet ou um ponto de extremidade de aplicativo que está em sua VNet. Se você implantar o ASE com um ponto de extremidade acessível pela Internet, essa implantação será chamada de ASE externo. Se você implantar o ASE com um ponto de extremidade em sua VNet, essa implantação será chamada de ASE ILB. Você pode saber mais sobre o ASE ILB no documento [criar e usar um ase ILB](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase) .

O ASE é um sistema de locatário único. Como é um único locatário, há alguns recursos disponíveis apenas com um ASE que não está disponível no serviço de aplicativo multilocatário. 

## <a name="ilb-ase-certificates"></a>Certificados do ASE ILB 

Se você estiver usando um ASE externo, seus aplicativos serão alcançados em [AppName]. [asename]. p. azurewebsites. net. Por padrão, todos os ASEs, até mesmo ILB ASEs, são criados com certificados que seguem esse formato. Quando você tiver um ASE ILB, os aplicativos serão alcançados com base no nome de domínio que você especificar ao criar o ASE ILB. Para que os aplicativos ofereçam suporte a SSL, você precisa carregar certificados. Obtenha um certificado SSL válido usando autoridades de certificação internas, comprando um certificado de um emissor externo ou usando um certificado autoassinado. 

Há duas opções para configurar certificados com o ASE ILB.  Você pode definir um certificado padrão de caractere curinga para o ASE ILB ou definir certificados nos aplicativos Web individuais no ASE.  Independentemente da escolha que você fizer, os seguintes atributos de certificado devem ser configurados corretamente:

- **Assunto:** Esse atributo deve ser definido como *. [Your-raiz-Domain-aqui] para um certificado de ASE ILB curinga. Se você estiver criando o certificado para seu aplicativo, ele deverá ser [AppName]. [Your-raiz-Domain-aqui]
- **Nome alternativo da entidade:** Esse atributo deve incluir *. [Your-raiz-Domain-aqui] e *. SCM. [Your-raiz-Domain-aqui] para o certificado curinga ILB ASE. Se você estiver criando o certificado para seu aplicativo, ele deverá ser [AppName]. [Your-raiz-Domain-aqui] e [AppName]. SCM. [Your-raiz-Domain-aqui].

Como uma terceira variante, você pode criar um certificado ASE ILB que inclui todos os nomes de aplicativo individuais na SAN do certificado em vez de usar uma referência de curinga. O problema com esse método é que você precisa saber antecipadamente os nomes dos aplicativos que você está colocando no ASE ou você precisa continuar atualizando o certificado ASE ILB.

### <a name="upload-certificate-to-ilb-ase"></a>Carregar o certificado para o ASE ILB 

Depois que um ASE ILB é criado no portal, o certificado deve ser definido para o ASE ILB. Até que o certificado seja definido, o ASE mostrará uma faixa informando que o certificado não foi definido.  

O certificado que você carrega deve ser um arquivo. pfx. Depois que o certificado for carregado, o ASE executará uma operação de dimensionamento para definir o certificado. 

Você não pode criar o ASE e carregar o certificado como uma ação no portal ou até mesmo em um modelo. Como uma ação separada, você pode carregar o certificado usando um modelo, conforme descrito no documento [criar um ase a partir de um modelo](./create-from-template.md) .  

Se você quiser criar um certificado autoassinado rapidamente para teste, poderá usar o seguinte bit do PowerShell:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Ao criar um certificado autoassinado, você precisará garantir que o nome da entidade tenha o formato CN = {ASE_NAME_HERE} _InternalLoadBalancingASE.

## <a name="application-certificates"></a>Certificados de aplicativo 

Os aplicativos hospedados em um ASE podem usar os recursos de certificado centrado no aplicativo que estão disponíveis no serviço de aplicativo multilocatário. Esses recursos incluem:  

- Certificados SNI 
- SSL baseado em IP, que tem suporte apenas com um ASE externo.  Um ASE ILB não dá suporte a SSL baseado em IP.
- Certificados hospedados do keyvault 

As instruções para carregar e gerenciar esses certificados estão disponíveis em [Adicionar um certificado SSL no serviço de Azure app](../configure-ssl-certificate.md).  Se você estiver simplesmente Configurando certificados para corresponder a um nome de domínio personalizado que você atribuiu ao seu aplicativo Web, essas instruções serão suficientes. Se você estiver carregando o certificado para um aplicativo Web ILB ASE com o nome de domínio padrão, especifique o site SCM na SAN do certificado, conforme observado anteriormente. 

## <a name="tls-settings"></a>Configurações de TLS 

Você pode definir a configuração de TLS em um nível de aplicativo.  

## <a name="private-client-certificate"></a>Certificado de cliente privado 

Um caso de uso comum é configurar seu aplicativo como um cliente em um modelo de cliente-servidor. Se você proteger o servidor com um certificado de autoridade de certificação privada, será necessário carregar o certificado do cliente em seu aplicativo.  As instruções a seguir carregarão certificados para o trustStore dos trabalhos em que seu aplicativo está sendo executado. Se você carregar o certificado para um aplicativo, poderá usá-lo com outros aplicativos no mesmo plano do serviço de aplicativo sem carregar o certificado novamente.

Para carregar o certificado em seu aplicativo no ASE:

1. Gere um arquivo *. cer* para seu certificado. 
2. Vá para o aplicativo que precisa do certificado no portal do Azure
3. Vá para configurações de SSL no aplicativo. Clique em carregar certificado. Selecione público. Selecione computador local. Forneça um nome. Procure e selecione o arquivo *. cer* . Selecione carregar. 
4. Copie a impressão digital.
5. Vá para configurações do aplicativo. Crie uma configuração de aplicativo WEBSITE_LOAD_ROOT_CERTIFICATES com a impressão digital como o valor. Se você tiver vários certificados, poderá colocá-los na mesma configuração separada por vírgulas e nenhum espaço em branco como 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

O certificado estará disponível por todos os aplicativos no mesmo plano do serviço de aplicativo que o aplicativo, que configurou essa configuração. Se você precisar que ele esteja disponível para aplicativos em um plano do serviço de aplicativo diferente, será necessário repetir a operação de configuração do aplicativo em um aplicativo nesse plano do serviço de aplicativo. Para verificar se o certificado está definido, vá para o console do Kudu e emita o seguinte comando no console de depuração do PowerShell:

    dir cert:\localmachine\root

Para executar testes, você pode criar um certificado autoassinado e gerar um arquivo *. cer* com o PowerShell a seguir: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

